
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>Trash Separation Game</title>
  <style>
    body { font-family: Arial, sans-serif; background: #eef5db; color: #222; margin: 0; padding: 0; }
    .container { max-width: 700px; margin: 40px auto; background: #fff; border-radius: 12px; box-shadow: 0 2px 10px rgba(0,0,0,0.06); padding: 32px; }
    h1 { text-align: center; margin-bottom: 12px; }
    #game, .bin { display: flex; justify-content: center; align-items: center; flex-direction: column; }
    .trash { font-size: 1.4em; margin: 16px 0; padding: 10px 20px; border-radius: 8px; background: #d3e8a7; cursor: grab; }
    .bins { display: flex; justify-content: space-around; margin-top: 22px;}
    .bin { width: 120px; height: 120px; border: 2px solid #9ec96b; border-radius: 10px; background: #f5eee1; margin: 0 8px; }
    .bin label { display: block; font-weight: bold; margin-bottom: 8px; }
    #score { text-align: center; margin-top: 24px; font-size: 1.2em; }
    #next-btn, #restart-btn { padding: 10px 24px; font-size: 1em; margin: 18px auto; display: block; border: none; background: #9ec96b; color: #fff; border-radius: 8px; cursor: pointer;}
    #next-btn:disabled { background: #ccc; }
  </style>
</head>
<body>
  <div class="container">
    <h1>Trash Separation Game</h1>
    <div id="game">
      <div id="trash-item" class="trash" draggable="true"></div>
      <div class="bins">
        <div class="bin" data-bin="recycle"><label>Recycle ♻️</label></div>
        <div class="bin" data-bin="organic"><label>Organic 🌱</label></div>
        <div class="bin" data-bin="general"><label>General 🗑️</label></div>
      </div>
    </div>
    <div id="score"></div>
    <button id="next-btn" disabled>Next</button>
    <button id="restart-btn" style="display:none;">Restart Game</button>
  </div>
  <script>
    // Trash items and their correct bin
    const items = [
      { name: 'Plastic Bottle', bin: 'recycle' },
      { name: 'Apple Core', bin: 'organic' },
      { name: 'Aluminum Can', bin: 'recycle' },
      { name: 'Banana Peel', bin: 'organic' },
      { name: 'Styrofoam Cup', bin: 'general' },
      { name: 'Old Newspaper', bin: 'recycle' },
      { name: 'Chicken Bones', bin: 'organic' },
      { name: 'Chip Bag', bin: 'general' },
      { name: 'Glass Jar', bin: 'recycle' },
      { name: 'Tea Bag', bin: 'organic' },
    ];

    let current = 0;
    let score = 0;

    const trashEl = document.getElementById('trash-item');
    const bins = document.querySelectorAll('.bin');
    const scoreEl = document.getElementById('score');
    const nextBtn = document.getElementById('next-btn');
    const restartBtn = document.getElementById('restart-btn');

    function showItem(index) {
      trashEl.textContent = items[index].name;
      nextBtn.disabled = true;
      bins.forEach(bin => {
        bin.style.borderColor = '#9ec96b';
        bin.style.backgroundColor = "#f5eee1";
      });
    }

    function updateScore() {
      scoreEl.textContent = `Score: ${score} / ${items.length}`;
    }

    function endGame() {
      trashEl.textContent = "Game Over!";
      scoreEl.textContent = `Final Score: ${score} / ${items.length}`;
      nextBtn.style.display = "none";
      restartBtn.style.display = "block";
    }

    // Drag events
    trashEl.addEventListener('dragstart', function(ev) {
      ev.dataTransfer.setData('text/plain', items[current].bin);
    });

    bins.forEach(bin => {
      bin.addEventListener('dragover', function(ev) {
        ev.preventDefault();
      });

      bin.addEventListener('drop', function(ev) {
        ev.preventDefault();
        let rightBin = items[current].bin;
        let chosenBin = this.getAttribute('data-bin');
        if(chosenBin === rightBin) {
          this.style.borderColor = 'green';
          this.style.backgroundColor = '#ddecc2';
          score++;
          scoreEl.textContent = "Correct!";
        } else {
          this.style.borderColor = 'red';
          this.style.backgroundColor = '#fdd';
          scoreEl.textContent = "Incorrect!";
        }
        nextBtn.disabled = false;
        updateScore();
      });
    });

    nextBtn.addEventListener('click', function() {
      current++;
      if(current < items.length){
        showItem(current);
        scoreEl.textContent = '';
      } else {
        endGame();
      }
    });

    restartBtn.addEventListener('click', function() {
      current = 0; score = 0;
      restartBtn.style.display = "none";
      nextBtn.style.display = "block";
      updateScore();
      showItem(current);
      scoreEl.textContent = '';
    });

    // Start game
    updateScore();
    showItem(current);
  </script>
</body>
</html>
