<template>
  <div id="canvas">
    <div id="deep-space" />
    <div id="menu" v-show="isGameRunning()">
      <button class="button-53" role="button" @click="resumeGame()">Começar / Resumir</button>
      <div style="height: 15px"/>
      <button class="button-53" role="button" @click="toggleScoreBoard()">Placar</button>
      <div style="height: 15px"/>
      <button class="button-53" role="button" @click="exitGame()">Sair</button>
      <div style="height: 15px"/>
      <div id="score-board" v-show="scoreBoardStatus()">
        <ol>
          <li>
            Id da partida: {{scoreBoard[0].matchId}} <br>
            Início da partida: {{scoreBoard[0].startTime}} <br>
            Asteróides destruídos: {{scoreBoard[0].nAsteroidsDestroyed}} <br>
            Score: {{scoreBoard[0].score}} <br>
          </li><br>
          <li>
            Id da partida: {{scoreBoard[1].matchId}} <br>
            Início da partida: {{scoreBoard[1].startTime}} <br>
            Asteróides destruídos: {{scoreBoard[1].nAsteroidsDestroyed}} <br>
            Score: {{scoreBoard[1].score}} <br>
          </li><br>
          <li>
            Id da partida: {{scoreBoard[2].matchId}} <br>
            Início da partida: {{scoreBoard[2].startTime}} <br>
            Asteróides destruídos: {{scoreBoard[2].nAsteroidsDestroyed}} <br>
            Score: {{scoreBoard[2].score}} <br>
          </li>
        </ol>
      </div>
    </div>
    <div id="space-field">
      <SpaceObject id="spaceship" class="spaceship" :data="spaceField.ship" resolution="2" />

      <SpaceObject class="asteroid" :data="asteroid" resolution="2"
                  :key="asteroid.center"
                  v-for="asteroid in spaceField.asteroids" />

      <SpaceObject class="missile" :data="missile" resolution="2"
                  :key="missile.center"
                  v-for="missile in spaceField.missiles" />

      <SpaceObject class="explosion" :data="explosion" resolution="2"
                  :key="explosion.center"
                  v-for="explosion in spaceField.explosions" />
    </div>
  </div>
</template>

<script setup>
import {ref} from 'vue'

let gameIsRunning = ref(false)
let showScoreBoard = ref(false)
let scoreBoard = [
  {
    matchId: 0,
    startTime: "2023-07-18",
    nAsteroidsDestroyed: 17,
    score: 170
  },
  {
    matchId: 2,
    startTime: "2023-07-18",
    nAsteroidsDestroyed: 10,
    score: 100
  },
  {
    matchId: 6,
    startTime: "2023-07-19",
    nAsteroidsDestroyed: 2,
    score: 20
  }
]

async function toggleScoreBoard() {
  await $post("/score-board")
  showScoreBoard = !showScoreBoard
}

function scoreBoardStatus() {return showScoreBoard}

async function sendPauseCommand() {
  const command = "PAUSE_GAME";
  console.log(`Triggering command: ${command}`);
  await $post("/ship/commands", { command })
}

function resumeGame() {
  gameIsRunning = !gameIsRunning
  sendPauseCommand()
}

async function exitGame() {
  await $post("/end-game")
}

function isGameRunning() {return gameIsRunning}

const {
  data: spaceField,
  refresh: updateSpaceField
} = await $get("/space-field");

onMounted(() => {
  window.addEventListener("keydown", async (event) => {
    if (event.code == "Escape") {
      gameIsRunning = !gameIsRunning
      await $post("/score-board")
    }
    const keyToCommand = {
      "ArrowUp": "MOVE_SHIP_UP",
      "ArrowDown": "MOVE_SHIP_DOWN",
      "ArrowRight": "MOVE_SHIP_RIGHT",
      "ArrowLeft": "MOVE_SHIP_LEFT",
      "Space": "LAUNCH_MISSILE",
      "Escape": "PAUSE_GAME",
    };

    const command = keyToCommand[event.code];

    // Ignore if invalid key was pressed
    if (command === undefined) return;

    console.log(`Triggering command: ${command}`);
    await $post("/ship/commands", { command })
  });

  window.setInterval(updateSpaceField, 1000);
})

</script>

<style>
#menu {
  position: relative;
  top: 10%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 10;
  display: flex;
  flex-direction: column;
  align-items: center;
}


#score-board {
  position: relative;
  color: white;
}

#canvas {
  height: calc(100vh - 4rem);
  width: calc(100vw - 4rem);

  padding: 2rem;

  background-color: #36bbf5;
  overflow: hidden;

  position: relative;

  display: flex;
  justify-content: center;
  align-items: center;
}

@keyframes slide {
  0% {
    transform: translate(1px);
  }
  50% {
    transform: translate(-1px);
  }
  100% {
    transform: translate(1px);
  }
}

#deep-space {
  height: calc(100% - 4rem);
  width: calc(100% - 4rem);

  background-image: url("~/assets/space.png");
  background-origin: content-box;
  animation: slide 3s linear infinite;

  position: absolute;
  z-index: 0;
}

#space-field {
  height: calc(100% - 4rem);
  width: calc(100% - 4rem);

  position: relative;
}

.spaceship {
  background-image: url("~/assets/spaceship.png");
}

.asteroid {
  background-image: url("~/assets/asteroid.png");
}

.missile {
  background-image: url("~/assets/missile.png");
}

.explosion {
  background-image: url("~/assets/explosion.png");
}


/* FONTE https://getcssscan.com/css-buttons-examples */
.button-53 {
  background-color: #3DD1E7;
  border: 0 solid #E5E7EB;
  box-sizing: border-box;
  color: #000000;
  display: flex;
  font-family: ui-sans-serif,system-ui,-apple-system,system-ui,"Segoe UI",Roboto,"Helvetica Neue",Arial,"Noto Sans",sans-serif,"Apple Color Emoji","Segoe UI Emoji","Segoe UI Symbol","Noto Color Emoji";
  font-size: 1rem;
  font-weight: 700;
  justify-content: center;
  line-height: 1.75rem;
  padding: .75rem 1.65rem;
  position: relative;
  text-align: center;
  text-decoration: none #000000 solid;
  text-decoration-thickness: auto;
  width: 100%;
  max-width: 460px;
  position: relative;
  cursor: pointer;
  transform: rotate(-2deg);
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
}

.button-53:focus {
  outline: 0;
}

.button-53:after {
  content: '';
  position: absolute;
  border: 1px solid #000000;
  bottom: 4px;
  left: 4px;
  width: calc(100% - 1px);
  height: calc(100% - 1px);
}

.button-53:hover:after {
  bottom: 2px;
  left: 2px;
}

@media (min-width: 768px) {
  .button-53 {
    padding: .75rem 3rem;
    font-size: 1.25rem;
  }
}
</style>
