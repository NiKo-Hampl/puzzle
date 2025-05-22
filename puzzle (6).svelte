<script>
  let grid = createShuffledGrid();
  let size = 3;

  let startTime = null;
  let elapsedSeconds = 0;
  let solved = false;
  let rating = "";

  let interval;
//algoritmus pro náhoné rozložení pole
  function createShuffledGrid() {
    let numbers = [1, 2, 3, 4, 5, 6, 7, 8, ''];
    let shuffled;

    do {
      shuffled = [...numbers];
      for (let i = shuffled.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
        [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
      }
    } while (!isSolvable(shuffled));

    return shuffled;
  }
//kontrola správnosti a nsatevní časovače
  function moveTile(index) {
    if (!startTime) {
      startTime = Date.now();
      interval = setInterval(() => {
        elapsedSeconds = Math.floor((Date.now() - startTime) / 1000);//kolik sekund od začátku

        if (elapsedSeconds > 120 && !solved) {// && musí platit obě podmínky najednou
          clearInterval(interval);
          solved = true;
          rating = "Zkus to znovu.";
        }
      }, 1000);
    }

    let emptyIndex = grid.indexOf('');
    if (areNeighbors(index, emptyIndex)) {
      [grid[index], grid[emptyIndex]] = [grid[emptyIndex], grid[index]];
    }

    if (!solved && isSolved()) {
      solved = true;
      clearInterval(interval);
      elapsedSeconds = Math.floor((Date.now() - startTime) / 1000);
      evaluatePerformance(elapsedSeconds);
    }
  }
//funkce aby poznali co je sousední dlaždice podle os x,y
  function areNeighbors(i1, i2) {
    let x1 = i1 % size;
    let y1 = Math.floor(i1 / size);
    let x2 = i2 % size;
    let y2 = Math.floor(i2 / size);
    return Math.abs(x1 - x2) + Math.abs(y1 - y2) === 1;
  }

  function isSolvable(arr) {//je možné to vyřešit?
    let nums = arr.filter(n => n !== '');//"odstraní" prázdné políčko z pole
    let inversions = 0;//inverze(větší číslo předchází menšímu)
    for (let i = 0; i < nums.length; i++) {//prochází každý prvek
      for (let j = i + 1; j < nums.length; j++) {//porovnává se všemi následujícími prvky
        if (nums[i] > nums[j]) inversions++;//Pokud je číslo na pozici i větší než číslo na pozici j, přičteme 1
      }
    }
    return inversions % 2 === 0;
  }

  function isSolved() {
    let correct = [1, 2, 3, 4, 5, 6, 7, 8, ''];
    return grid.every((val, idx) => val === correct[idx]);
  }
//ohodnocení podle výkonu
  function evaluatePerformance(seconds) {
    if (seconds <= 60) {
      rating = "Skvělá práce! +3 body int.";
    } else if (seconds <= 90) {
      rating = "Dobrá práce! +2 body int.";
    } else if (seconds <= 120) {
      rating = "Ujde to! +1 body int.";
    } 
  }
//funkce pro vynulování hry
  function restartGame() {
    clearInterval(interval);
    grid = createShuffledGrid();
    startTime = null;
    elapsedSeconds => 0;
    solved = false;
    rating = "";
  }
</script>

<h2>Jednoduché Puzzle 3×3</h2>
<button on:click={restartGame}>🔁 Restartovat</button>
<div class="game-container">
  <!-- Puzzle -->
  <div class="grid">
    {#each grid as tile, i}
      <div class="tile {tile === '' ? 'empty' : ''}" on:click={() => moveTile(i)}>
        {tile}
      </div>
    {/each}
  </div>

  <!-- Časovač + výsledek -->
  <div class="info">
    <p><strong>Čas:</strong> {elapsedSeconds} s</p>
    {#if solved}
      <p><strong>{rating}</strong>🧠</p>
    {/if}
  </div>
</div>

<style>
  .game-container {
    display: flex;
    align-items: flex-start;
    gap: 20px;
    margin-top: 10px;
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(3, 80px);
    gap: 5px;
    margin-bottom: 10px;
  }

  .tile {
		color: black;
    width: 80px;
    height: 80px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 24px;
    background-color: #f0f0f0;
    border: 2px solid #ccc;
    cursor: pointer;
    user-select: none;
  }

  .tile.empty {
    background-color: #fff;
    border: 2px dashed #aaa;
    cursor: default;
  }

  .info {
    font-size: 18px;
    min-width: 150px;
  }
  button {
    padding: 8px 16px;
    font-size: 16px;
    margin-bottom: 10px;
    cursor: pointer;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 5px;
    transition: background-color 0.2s;
  }

  button:hover {
    background-color: #0056b3;
  }
</style>