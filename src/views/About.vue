<template>
<section id="monster" class="container">
    <h2>Monster Health</h2>
    <div class="healthbar">
        <div class="healthbar__value" :style="monsterHealthStyle"></div>
    </div>
</section>

<section id="player" class="container">
    <h2>Your Health</h2>
    <div class="healthbar">
        <div class="healthbar__value" :style="playerHealthStyle"></div>
    </div>
</section>

<section class="container" v-if="winner">
  <h2>Game Over!</h2>
  <h3 v-if="winner === 'monster'">You Lost!</h3>
  <h3 v-else-if="winner === 'player'">You Won!</h3>
  <h3 v-else>Draw</h3>
  <button @click="restart">Restart</button>
</section>

<section id="controls" v-else class="container">
    <button @click="attackMonser">ATTACK</button>
    <button @click="specialAttack" :disabled="specialAttackEnabled">SPECIAL ATTACK</button>
    <button @click="healPlayer" >HEAL</button>
    <button @click="surrender">SURRENDER</button>
</section>

<section id="log" class="container">
    <h2>Battle Log</h2>
    <ul>
      <li v-for="(log, index) in logs" :key="index">
        <span>{{log.subject}} - {{log.action}} - {{log.value}}</span>
      </li>
    </ul>
</section>
</template>

<script>

function getRandomval (min, max) {
  return Math.floor(Math.random() * (max - min) + min)
}

export default {
  data () {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      currentRounds: 0,
      winner: null,
      logs: []
    }
  },
  computed: {
    playerHealthStyle () {
      const health = this.playerHealth <= 0 ? 0 : this.playerHealth
      return { width: health + '%' }
    },
    monsterHealthStyle () {
      const health = this.monsterHealth <= 0 ? 0 : this.monsterHealth
      return { width: health + '%' }
    },
    specialAttackEnabled () {
      return !(this.currentRounds % 3 === 0 && this.currentRounds !== 0)
    }
  },
  watch: {
    playerHealth (value) {
      if (value <= 0 && this.monsterHealth <= 0) {
        this.winner = 'draw'
      } else if (value <= 0) {
        this.winner = 'monster'
      }
    },
    monsterHealth (value) {
      if (value <= 0 && this.playerHealth <= 0) {
        this.winner = 'draw'
      } else if (value <= 0) {
        this.winner = 'player'
      }
    }
  },
  methods: {
    attackMonser () {
      this.currentRounds++
      const attackValue = getRandomval(5, 12)
      this.monsterHealth -= attackValue
      this.addLog('Player', 'Attack', attackValue)
      this.attackPlayer()
    },
    attackPlayer () {
      const attackValue = getRandomval(8, 12)
      this.playerHealth -= attackValue
      this.addLog('Monster', 'Attack', attackValue)
    },
    specialAttack () {
      this.currentRounds++
      const attackValue = getRandomval(10, 25)
      this.monsterHealth -= attackValue
      this.addLog('Player', 'Attack', attackValue)
      this.attackPlayer()
    },
    healPlayer () {
      this.currentRounds++
      const healValue = getRandomval(8, 20)
      this.playerHealth + healValue > 100 ? this.playerHealth = 100 : this.playerHealth += healValue
      this.addLog('Player', 'Heal', healValue)
      this.attackPlayer()
    },
    surrender () {
      this.addLog('Player', 'Surrender', null)
      this.winner = 'monster'
    },
    restart () {
      this.playerHealth = 100
      this.monsterHealth = 100
      this.currentRounds = 0
      this.winner = null
    },
    addLog (subject, action, value) {
      this.logs.unshift({ subject, action, value })
    }
  }
}
</script>

<style lang="css">
section {
    width: 90%;
    max-width: 40rem;
    margin: auto;
}

.healthbar {
    width: 100%;
    height: 40px;
    border: 1px solid #575757;
    margin: 1rem 0;
    background: #fde5e5;
}

.healthbar__value {
    background-color: #00a876;
    width: 100%;
    height: 100%;
}

.container {
    text-align: center;
    padding: 0.5rem;
    margin: 1rem auto;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
    border-radius: 12px;
}

#monster h2,
#player h2 {
    margin: 0.25rem;
}

#controls {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    align-items: center;
    justify-content: center;
}

button {
    font: inherit;
    border: 1px solid #88005b;
    background-color: #88005b;
    color: white;
    padding: 1rem 2rem;
    border-radius: 12px;
    margin: 1rem;
    width: 12rem;
    cursor: pointer;
    box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
}

button:focus {
    outline: none;
}

button:hover,
button:active {
    background-color: #af0a78;
    border-color: #af0a78;
    box-shadow: 1px 1px 8px rgba(0, 0, 0, 0.26);
}

button:disabled {
    background-color: #ccc;
    border-color: #ccc;
    box-shadow: none;
    color: #3f3f3f;
    cursor: not-allowed;
}

#log ul {
    list-style: none;
    margin: 0;
    padding: 0;
}

#log li {
    margin: 0.5rem 0;
}

.log--player {
    color: #7700ff;
}

.log--monster {
    color: #da8d00;
}

.log--damage {
    color: red;
}

.log--heal {
    color: green;
}
</style>
