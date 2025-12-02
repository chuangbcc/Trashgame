
<html lang="en">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Trash Separation Game</title>
  <script src="/_sdk/element_sdk.js"></script>
  <style>
    body {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Comic Sans MS', 'Arial', sans-serif;
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100%;
      overflow-x: hidden;
    }

    * {
      box-sizing: border-box;
    }

    .game-container {
      width: 100%;
      max-width: 1200px;
      padding: 32px;
      text-align: center;
    }

    .game-header {
      background: white;
      border-radius: 20px;
      padding: 24px;
      margin-bottom: 24px;
      box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
    }

    .game-title {
      font-size: 48px;
      font-weight: bold;
      margin: 0 0 16px 0;
      color: #667eea;
    }

    .instructions {
      font-size: 20px;
      color: #555;
      margin-bottom: 16px;
    }

    .game-stats {
      display: flex;
      justify-content: space-around;
      gap: 16px;
      margin-top: 16px;
      flex-wrap: wrap;
    }

    .stat-box {
      background: #f0f4ff;
      padding: 16px 24px;
      border-radius: 12px;
      flex: 1;
      min-width: 120px;
    }

    .stat-label {
      font-size: 14px;
      color: #666;
      margin-bottom: 4px;
    }

    .stat-value {
      font-size: 32px;
      font-weight: bold;
      color: #667eea;
    }

    .start-screen {
      background: white;
      border-radius: 20px;
      padding: 48px;
      box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
    }

    .start-button {
      background: #667eea;
      color: white;
      border: none;
      padding: 20px 48px;
      font-size: 28px;
      font-weight: bold;
      border-radius: 50px;
      cursor: pointer;
      transition: all 0.3s ease;
      box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
    }

    .start-button:hover {
      background: #5568d3;
      transform: translateY(-2px);
      box-shadow: 0 6px 16px rgba(102, 126, 234, 0.6);
    }

    .game-area {
      display: none;
    }

    .trash-item {
      background: white;
      border-radius: 16px;
      padding: 20px;
      margin: 12px;
      cursor: grab;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      font-size: 48px;
      width: 100px;
      height: 100px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      transition: all 0.2s ease;
      user-select: none;
    }

    .trash-item:hover {
      transform: scale(1.1);
      box-shadow: 0 6px 16px rgba(0, 0, 0, 0.2);
    }

    .trash-item.dragging {
      opacity: 0.5;
      cursor: grabbing;
    }

    .bins-container {
      display: flex;
      justify-content: center;
      gap: 16px;
      margin-top: 32px;
      flex-wrap: wrap;
    }

    .bin {
      background: white;
      border-radius: 20px;
      padding: 24px;
      width: 180px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      transition: all 0.3s ease;
    }

    .bin.drag-over {
      transform: scale(1.05);
      box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
    }

    .bin-icon {
      font-size: 64px;
      margin-bottom: 8px;
    }

    .bin-label {
      font-size: 18px;
      font-weight: bold;
      color: #333;
      margin-bottom: 16px;
    }

    .bin-items {
      min-height: 80px;
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    .bin-item {
      background: #f0f4ff;
      padding: 8px;
      border-radius: 8px;
      font-size: 24px;
    }

    .result-screen {
      display: none;
      background: white;
      border-radius: 20px;
      padding: 48px;
      box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
      position: relative;
    }

    .modal-overlay {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.7);
      z-index: 1000;
      justify-content: center;
      align-items: center;
    }

    .modal-content {
      background: white;
      border-radius: 20px;
      padding: 40px;
      text-align: center;
      max-width: 500px;
      box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
      animation: popIn 0.3s ease;
    }

    .modal-icon {
      font-size: 100px;
      margin-bottom: 20px;
      animation: shake 0.5s ease;
    }

    .modal-title {
      font-size: 36px;
      font-weight: bold;
      color: #e74c3c;
      margin-bottom: 16px;
    }

    .modal-message {
      font-size: 20px;
      color: #555;
      margin-bottom: 24px;
    }

    .modal-button {
      background: #e74c3c;
      color: white;
      border: none;
      padding: 16px 40px;
      font-size: 22px;
      font-weight: bold;
      border-radius: 50px;
      cursor: pointer;
      transition: all 0.3s ease;
    }

    .modal-button:hover {
      background: #c0392b;
      transform: translateY(-2px);
    }

    @keyframes popIn {
      0% {
        transform: scale(0.5);
        opacity: 0;
      }
      50% {
        transform: scale(1.1);
      }
      100% {
        transform: scale(1);
        opacity: 1;
      }
    }

    .result-icon {
      font-size: 120px;
      margin-bottom: 24px;
      animation: scaleIn 0.5s ease;
    }

    .result-title {
      font-size: 48px;
      font-weight: bold;
      margin-bottom: 16px;
      color: #667eea;
    }

    .result-message {
      font-size: 24px;
      color: #555;
      margin-bottom: 32px;
    }

    .play-again-button {
      background: #667eea;
      color: white;
      border: none;
      padding: 16px 40px;
      font-size: 24px;
      font-weight: bold;
      border-radius: 50px;
      cursor: pointer;
      transition: all 0.3s ease;
    }

    .play-again-button:hover {
      background: #5568d3;
      transform: translateY(-2px);
    }

    @keyframes scaleIn {
      from {
        transform: scale(0);
        opacity: 0;
      }
      to {
        transform: scale(1);
        opacity: 1;
      }
    }

    @keyframes shake {
      0%, 100% { transform: translateX(0); }
      10%, 30%, 50%, 70%, 90% { transform: translateX(-10px); }
      20%, 40%, 60%, 80% { transform: translateX(10px); }
    }

    @keyframes clap {
      0%, 100% { transform: scale(1); }
      50% { transform: scale(1.2); }
    }

    .clap-animation {
      animation: clap 0.3s ease infinite;
    }

    .bomb-animation {
      animation: shake 0.5s ease;
    }

    .trash-items-container {
      background: white;
      border-radius: 20px;
      padding: 24px;
      margin-bottom: 24px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      min-height: 140px;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-wrap: wrap;
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
  <script src="https://cdn.tailwindcss.com" type="text/javascript"></script>
 </head>
 <body>
  <div class="game-container">
   <div class="game-header">
    <h1 class="game-title" id="gameTitle">♻️ Trash Separation Game</h1>
    <p class="instructions" id="instructionsText">Drag each item to the correct bin! Sort 5 items at a time.</p>
    <div class="game-stats">
     <div class="stat-box">
      <div class="stat-label">
       Time
      </div>
      <div class="stat-value" id="timer">
       60
      </div>
     </div>
     <div class="stat-box">
      <div class="stat-label">
       Round
      </div>
      <div class="stat-value" id="round">
       0/4
      </div>
     </div>
     <div class="stat-box">
      <div class="stat-label">
       Score
      </div>
      <div class="stat-value" id="score">
       0
      </div>
     </div>
    </div>
   </div>
   <div class="start-screen" id="startScreen">
    <p style="font-size: 24px; color: #555; margin-bottom: 32px;">Ready to save the planet? 🌍</p><button class="start-button" id="startButton">Start Game</button>
   </div>
   <div class="game-area" id="gameArea">
    <div class="trash-items-container" id="trashContainer"></div>
    <div class="bins-container">
     <div class="bin" data-type="recyclable">
      <div class="bin-icon">
       ♻️
      </div>
      <div class="bin-label">
       Recyclable
      </div>
      <div class="bin-items" id="recyclable-items"></div>
     </div>
     <div class="bin" data-type="organic">
      <div class="bin-icon">
       🌿
      </div>
      <div class="bin-label">
       Organic
      </div>
      <div class="bin-items" id="organic-items"></div>
     </div>
     <div class="bin" data-type="general">
      <div class="bin-icon">
       🗑️
      </div>
      <div class="bin-label">
       General
      </div>
      <div class="bin-items" id="general-items"></div>
     </div>
     <div class="bin" data-type="hazardous">
      <div class="bin-icon">
       ⚠️
      </div>
      <div class="bin-label">
       Hazardous
      </div>
      <div class="bin-items" id="hazardous-items"></div>
     </div>
    </div>
   </div>
   <div class="result-screen" id="resultScreen">
    <div class="result-icon" id="resultIcon">
     💣
    </div>
    <div class="result-title" id="resultTitle">
     Time's Up!
    </div>
    <div class="result-message" id="resultMessage">
     Better luck next time!
    </div><button class="play-again-button" id="playAgainButton">Play Again</button>
   </div>
   <div class="modal-overlay" id="modalOverlay">
    <div class="modal-content">
     <div class="modal-icon">
      😅
     </div>
     <div class="modal-title">
      Oops! Try Again!
     </div>
     <div class="modal-message">
      Some items were sorted incorrectly. Give it another shot!
     </div><button class="modal-button" id="modalButton">Try Again</button>
    </div>
   </div>
  </div>
  <script>
    const defaultConfig = {
      game_title: "♻️ Trash Separation Game",
      instructions_text: "Drag each item to the correct bin! Sort 5 items at a time.",
      start_button_text: "Start Game"
    };

    const trashItems = [
      { emoji: "🥫", type: "recyclable" },
      { emoji: "📰", type: "recyclable" },
      { emoji: "🍾", type: "recyclable" },
      { emoji: "📦", type: "recyclable" },
      { emoji: "🥤", type: "recyclable" },
      { emoji: "🍌", type: "organic" },
      { emoji: "🍎", type: "organic" },
      { emoji: "🥬", type: "organic" },
      { emoji: "🍞", type: "organic" },
      { emoji: "🥕", type: "organic" },
      { emoji: "🧻", type: "general" },
      { emoji: "🧦", type: "general" },
      { emoji: "💄", type: "general" },
      { emoji: "🧸", type: "general" },
      { emoji: "👕", type: "general" },
      { emoji: "🔋", type: "hazardous" },
      { emoji: "💡", type: "hazardous" },
      { emoji: "🧴", type: "hazardous" },
      { emoji: "🎨", type: "hazardous" },
      { emoji: "🔌", type: "hazardous" }
    ];

    let gameState = {
      currentRound: 0,
      score: 0,
      timeLeft: 60,
      timer: null,
      currentItems: [],
      placements: {}
    };

    // Sound generation using Web Audio API
    const audioContext = new (window.AudioContext || window.webkitAudioContext)();

    function playSound(type) {
      const oscillator = audioContext.createOscillator();
      const gainNode = audioContext.createGain();
      
      oscillator.connect(gainNode);
      gainNode.connect(audioContext.destination);
      
      if (type === 'success') {
        // Happy ascending notes
        oscillator.frequency.setValueAtTime(523.25, audioContext.currentTime); // C5
        oscillator.frequency.setValueAtTime(659.25, audioContext.currentTime + 0.1); // E5
        oscillator.frequency.setValueAtTime(783.99, audioContext.currentTime + 0.2); // G5
        gainNode.gain.setValueAtTime(0.3, audioContext.currentTime);
        gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.4);
        oscillator.start(audioContext.currentTime);
        oscillator.stop(audioContext.currentTime + 0.4);
      } else if (type === 'error') {
        // Descending error sound
        oscillator.frequency.setValueAtTime(400, audioContext.currentTime);
        oscillator.frequency.setValueAtTime(200, audioContext.currentTime + 0.2);
        oscillator.type = 'sawtooth';
        gainNode.gain.setValueAtTime(0.3, audioContext.currentTime);
        gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.3);
        oscillator.start(audioContext.currentTime);
        oscillator.stop(audioContext.currentTime + 0.3);
      } else if (type === 'bomb') {
        // Explosion sound
        oscillator.frequency.setValueAtTime(100, audioContext.currentTime);
        oscillator.frequency.exponentialRampToValueAtTime(50, audioContext.currentTime + 0.5);
        oscillator.type = 'sawtooth';
        gainNode.gain.setValueAtTime(0.5, audioContext.currentTime);
        gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.5);
        oscillator.start(audioContext.currentTime);
        oscillator.stop(audioContext.currentTime + 0.5);
      } else if (type === 'clap') {
        // Clapping sound
        const bufferSize = audioContext.sampleRate * 0.1;
        const buffer = audioContext.createBuffer(1, bufferSize, audioContext.sampleRate);
        const data = buffer.getChannelData(0);
        for (let i = 0; i < bufferSize; i++) {
          data[i] = Math.random() * 2 - 1;
        }
        const noise = audioContext.createBufferSource();
        noise.buffer = buffer;
        const noiseGain = audioContext.createGain();
        noise.connect(noiseGain);
        noiseGain.connect(audioContext.destination);
        noiseGain.gain.setValueAtTime(0.3, audioContext.currentTime);
        noiseGain.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.1);
        noise.start(audioContext.currentTime);
        noise.stop(audioContext.currentTime + 0.1);
      } else if (type === 'place') {
        // Quick bloop sound for placing items
        oscillator.frequency.setValueAtTime(800, audioContext.currentTime);
        oscillator.type = 'sine';
        gainNode.gain.setValueAtTime(0.2, audioContext.currentTime);
        gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.1);
        oscillator.start(audioContext.currentTime);
        oscillator.stop(audioContext.currentTime + 0.1);
      }
    }

    function shuffleArray(array) {
      const shuffled = [...array];
      for (let i = shuffled.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
      }
      return shuffled;
    }

    function startGame() {
      gameState = {
        currentRound: 0,
        score: 0,
        timeLeft: 60,
        timer: null,
        currentItems: shuffleArray(trashItems),
        placements: {}
      };

      document.getElementById('startScreen').style.display = 'none';
      document.getElementById('gameArea').style.display = 'block';
      document.getElementById('resultScreen').style.display = 'none';

      loadRound();
      startTimer();
    }

    function loadRound() {
      gameState.currentRound++;
      const startIndex = (gameState.currentRound - 1) * 5;
      const roundItems = gameState.currentItems.slice(startIndex, startIndex + 5);

      const container = document.getElementById('trashContainer');
      container.innerHTML = '';

      roundItems.forEach((item, index) => {
        const itemDiv = document.createElement('div');
        itemDiv.className = 'trash-item';
        itemDiv.draggable = true;
        itemDiv.textContent = item.emoji;
        itemDiv.dataset.type = item.type;
        itemDiv.dataset.id = `item-${startIndex + index}`;
        
        itemDiv.addEventListener('dragstart', handleDragStart);
        itemDiv.addEventListener('dragend', handleDragEnd);
        
        container.appendChild(itemDiv);
      });

      document.querySelectorAll('.bin-items').forEach(bin => bin.innerHTML = '');
      gameState.placements = {};

      updateDisplay();
    }

    function startTimer() {
      gameState.timer = setInterval(() => {
        gameState.timeLeft--;
        updateDisplay();

        if (gameState.timeLeft <= 0) {
          endGame(false);
        }
      }, 1000);
    }

    function handleDragStart(e) {
      e.target.classList.add('dragging');
      e.dataTransfer.effectAllowed = 'move';
      e.dataTransfer.setData('text/html', e.target.innerHTML);
      e.dataTransfer.setData('item-id', e.target.dataset.id);
      e.dataTransfer.setData('item-type', e.target.dataset.type);
    }

    function handleDragEnd(e) {
      e.target.classList.remove('dragging');
    }

    document.querySelectorAll('.bin').forEach(bin => {
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

        const itemId = e.dataTransfer.getData('item-id');
        const itemType = e.dataTransfer.getData('item-type');
        const binType = bin.dataset.type;
        const itemEmoji = e.dataTransfer.getData('text/html');

        const itemElement = document.querySelector(`[data-id="${itemId}"]`);
        if (itemElement) {
          itemElement.remove();
        }

        gameState.placements[itemId] = {
          correct: itemType,
          placed: binType,
          emoji: itemEmoji
        };

        const binItems = bin.querySelector('.bin-items');
        const binItem = document.createElement('div');
        binItem.className = 'bin-item';
        binItem.textContent = itemEmoji;
        binItems.appendChild(binItem);

        playSound('place');

        if (Object.keys(gameState.placements).length === 5) {
          setTimeout(() => checkRound(), 300);
        }
      });
    });

    function checkRound() {
      const correctPlacements = Object.values(gameState.placements).filter(
        p => p.correct === p.placed
      ).length;

      const allCorrect = correctPlacements === 5;

      if (!allCorrect) {
        // Show try again modal
        playSound('error');
        showModal();
        return;
      }

      // All correct in this round
      gameState.score += correctPlacements * 20;
      playSound('success');

      if (gameState.currentRound < 4) {
        setTimeout(() => loadRound(), 500);
      } else {
        // All 4 rounds completed with all correct
        endGame(true);
      }
    }

    function showModal() {
      document.getElementById('modalOverlay').style.display = 'flex';
    }

    function hideModal() {
      document.getElementById('modalOverlay').style.display = 'none';
      // Reset the current round
      document.querySelectorAll('.bin-items').forEach(bin => bin.innerHTML = '');
      gameState.placements = {};
      loadRound();
      gameState.currentRound--;
    }

    function endGame(success) {
      clearInterval(gameState.timer);
      
      document.getElementById('gameArea').style.display = 'none';
      document.getElementById('resultScreen').style.display = 'block';

      const resultIcon = document.getElementById('resultIcon');
      const resultTitle = document.getElementById('resultTitle');
      const resultMessage = document.getElementById('resultMessage');

      if (gameState.timeLeft <= 0) {
        playSound('bomb');
        resultIcon.textContent = '💣';
        resultIcon.className = 'result-icon bomb-animation';
        resultTitle.textContent = 'Time\'s Up!';
        resultMessage.textContent = `You ran out of time! Score: ${gameState.score}`;
      } else if (success) {
        playSound('clap');
        setTimeout(() => playSound('clap'), 200);
        setTimeout(() => playSound('clap'), 400);
        resultIcon.textContent = '👏';
        resultIcon.className = 'result-icon clap-animation';
        resultTitle.textContent = 'Perfect!';
        resultMessage.textContent = `Amazing! You got everything right! Score: ${gameState.score}`;
      } else {
        resultIcon.textContent = '😊';
        resultIcon.className = 'result-icon';
        resultTitle.textContent = 'Good Try!';
        resultMessage.textContent = `Great effort! Score: ${gameState.score}`;
      }
    }

    function updateDisplay() {
      document.getElementById('timer').textContent = gameState.timeLeft;
      document.getElementById('round').textContent = `${gameState.currentRound}/4`;
      document.getElementById('score').textContent = gameState.score;
    }

    document.getElementById('startButton').addEventListener('click', startGame);
    document.getElementById('playAgainButton').addEventListener('click', startGame);
    document.getElementById('modalButton').addEventListener('click', hideModal);

    async function onConfigChange(config) {
      document.getElementById('gameTitle').textContent = config.game_title || defaultConfig.game_title;
      document.getElementById('instructionsText').textContent = config.instructions_text || defaultConfig.instructions_text;
      document.getElementById('startButton').textContent = config.start_button_text || defaultConfig.start_button_text;
    }

    if (window.elementSdk) {
      window.elementSdk.init({
        defaultConfig,
        onConfigChange,
        mapToCapabilities: () => ({
          recolorables: [],
          borderables: [],
          fontEditable: undefined,
          fontSizeable: undefined
        }),
        mapToEditPanelValues: (config) => new Map([
          ["game_title", config.game_title || defaultConfig.game_title],
          ["instructions_text", config.instructions_text || defaultConfig.instructions_text],
          ["start_button_text", config.start_button_text || defaultConfig.start_button_text]
        ])
      });
    }
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9a798446f08df4cf',t:'MTc2NDY2NDc5Ny4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
