
<html lang="en">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Trash Separation Game</title>
  <script src="/_sdk/element_sdk.js"></script>
  <style>
    body {
      box-sizing: border-box;
    }
    
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      width: 100%;
      height: 100%;
      overflow: hidden;
    }

    html {
      width: 100%;
      height: 100%;
    }

    .game-wrapper {
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      padding: 32px;
    }

    header {
      text-align: center;
      margin-bottom: 24px;
    }

    h1 {
      margin-bottom: 8px;
    }

    .instructions {
      font-size: 14px;
      opacity: 0.8;
    }

    main {
      flex: 1;
      display: flex;
      flex-direction: column;
      gap: 32px;
      min-height: 0;
    }

    .score-board {
      display: flex;
      justify-content: center;
      gap: 32px;
      padding: 16px;
      border-radius: 12px;
      font-weight: 600;
    }

    .score-item {
      display: flex;
      align-items: center;
      gap: 8px;
      font-size: 18px;
    }

    .items-area {
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 16px;
      flex-wrap: wrap;
      padding: 24px;
      border-radius: 16px;
      min-height: 120px;
    }

    .trash-item {
      padding: 16px 24px;
      border-radius: 12px;
      font-size: 48px;
      cursor: grab;
      transition: transform 0.2s, box-shadow 0.2s;
      user-select: none;
      box-shadow: 0 4px 12px rgba(0,0,0,0.15);
    }

    .trash-item:hover {
      transform: translateY(-4px);
      box-shadow: 0 6px 16px rgba(0,0,0,0.2);
    }

    .trash-item.dragging {
      opacity: 0.5;
      cursor: grabbing;
    }

    .bins-area {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 16px;
      padding: 0 16px;
    }

    .bin {
      border: 3px dashed;
      border-radius: 16px;
      padding: 24px;
      text-align: center;
      min-height: 180px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 8px;
      transition: all 0.3s;
    }

    .bin.drag-over {
      transform: scale(1.05);
      box-shadow: 0 8px 24px rgba(0,0,0,0.2);
    }

    .bin-icon {
      font-size: 48px;
    }

    .bin-label {
      font-weight: 600;
      font-size: 18px;
      margin-top: 8px;
    }

    .bin-count {
      font-size: 14px;
      opacity: 0.7;
      font-weight: 500;
    }

    .success-modal {
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      padding: 48px;
      border-radius: 24px;
      text-align: center;
      box-shadow: 0 12px 48px rgba(0,0,0,0.3);
      display: none;
      z-index: 1000;
    }

    .success-modal.show {
      display: block;
      animation: popIn 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
    }

    @keyframes popIn {
      0% {
        transform: translate(-50%, -50%) scale(0);
      }
      100% {
        transform: translate(-50%, -50%) scale(1);
      }
    }

    .success-icon {
      font-size: 72px;
      margin-bottom: 16px;
    }

    .success-modal h2 {
      font-size: 32px;
      margin-bottom: 16px;
    }

    .success-modal p {
      font-size: 18px;
      margin-bottom: 24px;
      opacity: 0.9;
    }

    .reset-button {
      padding: 14px 32px;
      border: none;
      border-radius: 12px;
      font-size: 16px;
      font-weight: 600;
      cursor: pointer;
      transition: all 0.3s;
      box-shadow: 0 4px 12px rgba(0,0,0,0.15);
    }

    .reset-button:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 16px rgba(0,0,0,0.2);
    }

    @media (max-width: 768px) {
      .game-wrapper {
        padding: 16px;
      }

      .bins-area {
        grid-template-columns: 1fr;
      }

      .trash-item {
        font-size: 36px;
        padding: 12px 20px;
      }

      h1 {
        font-size: 24px;
      }
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
  <script src="https://cdn.tailwindcss.com" type="text/javascript"></script>
 </head>
 <body>
  <div class="game-wrapper">
   <header>
    <h1 id="game-title">♻️ Trash Separation Challenge</h1>
    <p class="instructions" id="instructions">Drag each item to the correct recycling bin!</p>
   </header>
   <main>
    <div class="score-board" id="score-board">
     <div class="score-item"><span>✅</span> <span>Correct: <span id="correct-count">0</span></span>
     </div>
     <div class="score-item"><span>❌</span> <span>Wrong: <span id="wrong-count">0</span></span>
     </div>
    </div>
    <div class="items-area" id="items-area"></div>
    <div class="bins-area" id="bins-area">
     <div class="bin" data-type="recyclable">
      <div class="bin-icon">
       ♻️
      </div>
      <div class="bin-label">
       Recyclable
      </div>
      <div class="bin-count">
       0 items
      </div>
     </div>
     <div class="bin" data-type="organic">
      <div class="bin-icon">
       🌱
      </div>
      <div class="bin-label">
       Organic
      </div>
      <div class="bin-count">
       0 items
      </div>
     </div>
     <div class="bin" data-type="general">
      <div class="bin-icon">
       🗑️
      </div>
      <div class="bin-label">
       General Waste
      </div>
      <div class="bin-count">
       0 items
      </div>
     </div>
    </div>
   </main>
   <div class="success-modal" id="success-modal">
    <div class="success-icon">
     🎉
    </div>
    <h2 id="success-title">Great Job!</h2>
    <p id="success-message">You've sorted all the trash correctly!</p><button class="reset-button" id="reset-button">Play Again</button>
   </div>
  </div>
  <script>
    const defaultConfig = {
      background_color: "#e8f5e9",
      surface_color: "#ffffff",
      text_color: "#2e7d32",
      primary_action_color: "#4caf50",
      secondary_action_color: "#81c784",
      game_title: "♻️ Trash Separation Challenge",
      instructions_text: "Drag each item to the correct recycling bin!",
      success_message: "You've sorted all the trash correctly!",
      font_family: "Segoe UI",
      font_size: 16
    };

    let config = { ...defaultConfig };

    const trashItems = [
      { emoji: "🍎", type: "organic", name: "apple" },
      { emoji: "📰", type: "recyclable", name: "newspaper" },
      { emoji: "🥤", type: "recyclable", name: "can" },
      { emoji: "🍌", type: "organic", name: "banana" },
      { emoji: "📦", type: "recyclable", name: "cardboard" },
      { emoji: "🍕", type: "organic", name: "pizza" },
      { emoji: "💡", type: "general", name: "lightbulb" },
      { emoji: "🥫", type: "recyclable", name: "tin" },
      { emoji: "🍞", type: "organic", name: "bread" },
      { emoji: "🧦", type: "general", name: "sock" },
      { emoji: "🍾", type: "recyclable", name: "bottle" },
      { emoji: "🥕", type: "organic", name: "carrot" }
    ];

    let currentItems = [];
    let correctCount = 0;
    let wrongCount = 0;
    let draggedItem = null;
    let binCounts = { recyclable: 0, organic: 0, general: 0 };

    function initGame() {
      currentItems = shuffleArray([...trashItems]).slice(0, 6);
      correctCount = 0;
      wrongCount = 0;
      binCounts = { recyclable: 0, organic: 0, general: 0 };
      renderItems();
      updateScore();
      updateBinCounts();
      document.getElementById('success-modal').classList.remove('show');
    }

    function shuffleArray(array) {
      const newArray = [...array];
      for (let i = newArray.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [newArray[i], newArray[j]] = [newArray[j], newArray[i]];
      }
      return newArray;
    }

    function renderItems() {
      const itemsArea = document.getElementById('items-area');
      itemsArea.innerHTML = '';
      
      currentItems.forEach((item, index) => {
        const itemEl = document.createElement('div');
        itemEl.className = 'trash-item';
        itemEl.draggable = true;
        itemEl.textContent = item.emoji;
        itemEl.dataset.index = index;
        itemEl.dataset.type = item.type;
        
        itemEl.addEventListener('dragstart', handleDragStart);
        itemEl.addEventListener('dragend', handleDragEnd);
        
        itemsArea.appendChild(itemEl);
      });
    }

    function handleDragStart(e) {
      draggedItem = e.target;
      e.target.classList.add('dragging');
      e.dataTransfer.effectAllowed = 'move';
    }

    function handleDragEnd(e) {
      e.target.classList.remove('dragging');
    }

    function updateScore() {
      document.getElementById('correct-count').textContent = correctCount;
      document.getElementById('wrong-count').textContent = wrongCount;
    }

    function updateBinCounts() {
      const bins = document.querySelectorAll('.bin');
      bins.forEach(bin => {
        const type = bin.dataset.type;
        const count = binCounts[type];
        const countEl = bin.querySelector('.bin-count');
        countEl.textContent = `${count} ${count === 1 ? 'item' : 'items'}`;
      });
    }

    function checkWin() {
      if (currentItems.length === 0) {
        setTimeout(() => {
          document.getElementById('success-modal').classList.add('show');
        }, 500);
      }
    }

    const bins = document.querySelectorAll('.bin');
    bins.forEach(bin => {
      bin.addEventListener('dragover', (e) => {
        e.preventDefault();
        bin.classList.add('drag-over');
      });

      bin.addEventListener('dragleave', () => {
        bin.classList.remove('drag-over');
      });

      bin.addEventListener('drop', (e) => {
        e.preventDefault();
        bin.classList.remove('drag-over');
        
        if (draggedItem) {
          const itemType = draggedItem.dataset.type;
          const binType = bin.dataset.type;
          const itemIndex = parseInt(draggedItem.dataset.index);
          
          if (itemType === binType) {
            correctCount++;
            binCounts[binType]++;
            currentItems = currentItems.filter((_, i) => i !== itemIndex);
            draggedItem.style.animation = 'popIn 0.3s ease';
            setTimeout(() => {
              renderItems();
              updateBinCounts();
              checkWin();
            }, 300);
          } else {
            wrongCount++;
            draggedItem.style.animation = 'shake 0.5s ease';
            setTimeout(() => {
              if (draggedItem) draggedItem.style.animation = '';
            }, 500);
          }
          
          updateScore();
          draggedItem = null;
        }
      });
    });

    document.getElementById('reset-button').addEventListener('click', () => {
      initGame();
    });

    async function onConfigChange(newConfig) {
      const baseFont = newConfig.font_family || defaultConfig.font_family;
      const baseFontStack = 'Tahoma, Geneva, Verdana, sans-serif';
      const fontSize = newConfig.font_size || defaultConfig.font_size;

      document.body.style.fontFamily = `${baseFont}, ${baseFontStack}`;
      document.body.style.backgroundColor = newConfig.background_color || defaultConfig.background_color;
      document.body.style.color = newConfig.text_color || defaultConfig.text_color;

      document.querySelector('.game-wrapper').style.backgroundColor = newConfig.background_color || defaultConfig.background_color;

      document.getElementById('game-title').textContent = newConfig.game_title || defaultConfig.game_title;
      document.getElementById('game-title').style.fontSize = `${fontSize * 2}px`;
      document.getElementById('game-title').style.color = newConfig.text_color || defaultConfig.text_color;

      document.getElementById('instructions').textContent = newConfig.instructions_text || defaultConfig.instructions_text;
      document.getElementById('instructions').style.fontSize = `${fontSize * 0.875}px`;
      document.getElementById('instructions').style.color = newConfig.text_color || defaultConfig.text_color;

      document.querySelector('.score-board').style.backgroundColor = newConfig.surface_color || defaultConfig.surface_color;
      document.querySelector('.score-board').style.color = newConfig.text_color || defaultConfig.text_color;
      document.querySelector('.score-board').style.fontSize = `${fontSize * 1.125}px`;

      document.querySelector('.items-area').style.backgroundColor = newConfig.surface_color || defaultConfig.surface_color;

      const trashItems = document.querySelectorAll('.trash-item');
      trashItems.forEach(item => {
        item.style.backgroundColor = newConfig.secondary_action_color || defaultConfig.secondary_action_color;
        item.style.color = newConfig.text_color || defaultConfig.text_color;
        item.style.fontSize = `${fontSize * 3}px`;
      });

      const bins = document.querySelectorAll('.bin');
      bins.forEach(bin => {
        bin.style.borderColor = newConfig.primary_action_color || defaultConfig.primary_action_color;
        bin.style.backgroundColor = newConfig.surface_color || defaultConfig.surface_color;
      });

      const binLabels = document.querySelectorAll('.bin-label');
      binLabels.forEach(label => {
        label.style.color = newConfig.text_color || defaultConfig.text_color;
        label.style.fontSize = `${fontSize * 1.125}px`;
      });

      const binCounts = document.querySelectorAll('.bin-count');
      binCounts.forEach(count => {
        count.style.color = newConfig.text_color || defaultConfig.text_color;
        count.style.fontSize = `${fontSize * 0.875}px`;
      });

      const binIcons = document.querySelectorAll('.bin-icon');
      binIcons.forEach(icon => {
        icon.style.fontSize = `${fontSize * 3}px`;
      });

      document.getElementById('success-modal').style.backgroundColor = newConfig.surface_color || defaultConfig.surface_color;
      document.getElementById('success-modal').style.color = newConfig.text_color || defaultConfig.text_color;

      document.getElementById('success-title').style.fontSize = `${fontSize * 2}px`;
      document.getElementById('success-title').style.color = newConfig.text_color || defaultConfig.text_color;

      document.getElementById('success-message').textContent = newConfig.success_message || defaultConfig.success_message;
      document.getElementById('success-message').style.fontSize = `${fontSize * 1.125}px`;
      document.getElementById('success-message').style.color = newConfig.text_color || defaultConfig.text_color;

      const resetButton = document.getElementById('reset-button');
      resetButton.style.backgroundColor = newConfig.primary_action_color || defaultConfig.primary_action_color;
      resetButton.style.color = newConfig.surface_color || defaultConfig.surface_color;
      resetButton.style.fontSize = `${fontSize}px`;
    }

    if (window.elementSdk) {
      window.elementSdk.init({
        defaultConfig,
        onConfigChange,
        mapToCapabilities: (config) => ({
          recolorables: [
            {
              get: () => config.background_color || defaultConfig.background_color,
              set: (value) => {
                config.background_color = value;
                window.elementSdk.setConfig({ background_color: value });
              }
            },
            {
              get: () => config.surface_color || defaultConfig.surface_color,
              set: (value) => {
                config.surface_color = value;
                window.elementSdk.setConfig({ surface_color: value });
              }
            },
            {
              get: () => config.text_color || defaultConfig.text_color,
              set: (value) => {
                config.text_color = value;
                window.elementSdk.setConfig({ text_color: value });
              }
            },
            {
              get: () => config.primary_action_color || defaultConfig.primary_action_color,
              set: (value) => {
                config.primary_action_color = value;
                window.elementSdk.setConfig({ primary_action_color: value });
              }
            },
            {
              get: () => config.secondary_action_color || defaultConfig.secondary_action_color,
              set: (value) => {
                config.secondary_action_color = value;
                window.elementSdk.setConfig({ secondary_action_color: value });
              }
            }
          ],
          borderables: [],
          fontEditable: {
            get: () => config.font_family || defaultConfig.font_family,
            set: (value) => {
              config.font_family = value;
              window.elementSdk.setConfig({ font_family: value });
            }
          },
          fontSizeable: {
            get: () => config.font_size || defaultConfig.font_size,
            set: (value) => {
              config.font_size = value;
              window.elementSdk.setConfig({ font_size: value });
            }
          }
        }),
        mapToEditPanelValues: (config) => new Map([
          ["game_title", config.game_title || defaultConfig.game_title],
          ["instructions_text", config.instructions_text || defaultConfig.instructions_text],
          ["success_message", config.success_message || defaultConfig.success_message]
        ])
      });

      config = window.elementSdk.config;
    }

    initGame();
    onConfigChange(config);
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9a3791ab2611d32f',t:'MTc2Mzk3MzI4NS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
