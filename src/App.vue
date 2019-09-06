<template>

  <div id="app">

    <div id="oponent">
      <div id=oponentInfo>
        <h1 class="text-left"> {{monsterName}}</h1>
        <div class="healthbar">
          <div
           class="healthbar text-center"
            style="background-color: #5B5B5B; margin: 0; color: black; padding-top: 30px; font-size: 2rem;"
            :style="{width: playerHealth + '%'}">
            HP {{ playerHealth }} / 100
        </div>
        </div>
      </div>
      <div class="oponent-image">
        <img v-bind:src="pokeImage" alt="none">
      </div>
    </div>

  <div>
    <div id="oponent">
          <div class="player-image">
      <img v-bind:src="pokeImage" alt="none">
    </div>
      <div id="playerInfo">
        <h1 class="text-right"> {{playerName}}</h1>
        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="background-color: #5B5B5B; margin: 0; color: black; padding-top: 30px; font-size: 2rem;"
            :style="{width: monsterHealth + '%'}">
            HP {{ monsterHealth }} / 100
          </div>
        </div>
      </div>
    </div>
  </div>

  <section v-if="!gameIsRunning">
    <div class="player-input">
      <div id="battle-alert">
     <h1>Wild {{monsterName}}<br> appeared!</h1>
    </div> 
      <div id="start-buttons">
        <button id="attack" @click="fight">FIGHT</button>
        <button id="give-up" @click="giveUp">RUN</button>
      </div>
    </div>
  </section>

  <section v-else>
    <div class="battle-input">
      <div id="battle-buttons">
        <button id="attack" @click="attack">ATTACK</button>
        <button id="special-attack" @click="specialAttack">SPECIAL ATTACK</button>
        <button id="give-up" @click="giveUp">GIVE UP</button>
        <button id="heal" @click="heal">HEAL</button>
      </div>

    </div>
  </section>

  </div>



</template>

<!--
<template>
  <div id="app">

      <section class="row controls" v-if="!gameIsRunning">
          <div class="small-12 columns">
              <button id="start-game" @click="startGame">START NEW GAME</button>
          </div>
      </section>
      <section class="row controls" v-else>
          <div class="small-12 columns">
              <button id="attack" @click="attack">ATTACK</button>
              <button id="special-attack" @click="specialAttack">SPECIAL ATTACK</button>
              <button id="heal" @click="heal">HEAL</button>
              <button id="give-up" @click="giveUp">GIVE UP</button>
          </div>
      </section>
      <section class="row log" v-if="turns.length > 0">
          <div class="small-12 columns">
              <ul>
                  <li v-for="turn in turns"
                  v-bind:key="turn.id"
                      :class="{'player-turn': turn.isPlayer, 'monster-turn': !turn.isPlayer}">
                      {{ turn.text }}
                  </li>
              </ul>
          </div>
      </section>
  </div>


</template>

-->
<script>
import HelloWorld from './components/HelloWorld.vue'
import { pokemon } from './assets/pokedex'


console.log(pokemon[1].id)

let player1 = Math.floor(Math.random() * 151) + 0;
let player2 = Math.floor(Math.random() * 151) + 0;

let pokemonImage = `file://assets/images/${player1 + 1}.png`;

console.log(pokemonImage)
console.log(pokemon[player1].name.english)

var audio = new Audio('file://assets/victory_theme.ogg');

export default {
  name: 'app',
  components: {
    HelloWorld
  },
  data: function () {
    return {
        playerName: pokemon[player1].name.english,
        monsterName: pokemon[player2].name.english,
        playerHealth: 100,
        monsterHealth: 100,
        gameIsRunning: false,
        turns: [],
        pokeImage: pokemonImage,
    }
  },
    methods: {
        playVictoryTrack: function () {
          audio.play();
        },
        startGame: function () {
            this.gameIsRunning = false;
            this.playerHealth = 100;
            this.monsterHealth = 100;
            this.turns = [];
        },
        image: function () {
          return `assets/images/${player1}.png`
        },
        fight: function () {
          this.gameIsRunning = true;
        },
        attack: function () {
            var damage = this.calculateDamage(3, 10);
            this.monsterHealth -= damage;
            this.turns.unshift({
                isPlayer: true,
                text: `${this.playerName} hits ${this.monsterName} for ` + damage
            });
            if (this.checkWin()) {
                return;
            }

            this.monsterAttacks();
        },
        specialAttack: function () {
            var damage = this.calculateDamage(10, 20);
            this.monsterHealth -= damage;
            this.turns.unshift({
                isPlayer: true,
                text: `${this.playerName} used special attack on ${this.monsterName} for ` + damage
            });
            if (this.checkWin()) {
                return;
            }
            this.monsterAttacks();
        },
        heal: function () {
            if (this.playerHealth <= 90) {
                this.playerHealth += 10;
            } else {
                this.playerHealth = 100;
            }
            this.turns.unshift({
                isPlayer: true,
                text: `${this.playerName} heals for 10`
            });
            this.monsterAttacks();
        },
        giveUp: function () {
            location.reload();
        },
        monsterAttacks: function() {
            var damage = this.calculateDamage(5, 12);
            this.playerHealth -= damage;
            this.checkWin();
            this.turns.unshift({
                isPlayer: false,
                text: `${this.monsterName} hits ${this.playerName} for ` + damage
            });
        },
        calculateDamage: function(min, max) {
            return Math.max(Math.floor(Math.random() * max) + 1, min);
        },
        checkWin: function() {
            if (this.monsterHealth <= 0) {
                if (confirm('You won! New Game?')) {
                    this.startGame();
                } else {
                    this.gameIsRunning = false;
                }
                return true;
            } else if (this.playerHealth <= 0) {
                if (confirm('You lost! New Game?')) {
                    this.startGame();
                } else {
                    this.gameIsRunning = false;
                }
                return true;
            }
            return false;
        }
  
    }
}
</script>

<style>

#app {
  font-family: 'VT323', monospace;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 80vw;
  margin-left: auto;
  margin-right: auto;
  padding-top: 5%;
}

h1 {
  margin-bottom: 5px;
  font-size: 2rem;
  margin-top: 0;
}

#oponent {
  display: inline-block;
  width: 100%;
  margin-bottom: 20px;
}

#oponentInfo {
  float: left;
  width: 50%;
  padding-bottom: 10px;
}
#playerInfo {
  float: right;
  width: 50%;
  padding-bottom: 20px;
  margin-top: 60px;
}

.oponent-image {
  border: solid red;
  height: 175px;
  width: 40%;
  float: right;
  margin-right: 5%;
}
.oponent-image img {
  border: solid yellow;
    height: 175px;
    width: 40%;

}

.player-image {
  border: solid red;
  height: 175px;
  width: 40%;
  float: left;
  margin-left: 5%
}

.player-input {
  width: 100%;
  padding: 5px;
  margin-top: 20px;
  border: solid 10px black;
  border-style: double;
  border-radius: 5px;
  display: inline-block;
}

#battle-buttons {
  width: 50%;
}

#battle-buttons button {
  font-size: 2rem;
}

.battle-input {
  width: 100%;
  border: solid 10px black;
  border-style: double;
  border-radius: 5px;
  margin-top: 20px;
  padding: 5px;
}

.player-input h1 {
  text-align: left;
}

#battle-alert {
  float: left;
}

#start-buttons {
  float: right;
  display: block;
}

.text-center {
    text-align: center;
}

.text-left {
  text-align: left;
  padding-left: 5%;
}

.text-right {
  text-align: right;
  padding-right: 5%;
}


.healthbar {
    width: 90%;
    height: 30px;
    background-color: #eee;
    margin: auto;
    transition: width 500ms;
    border-radius: 15px;
    margin-left: 5%;
}

.controls, .log {
    margin-top: 30px;
    text-align: center;
    padding: 10px;
    border: 1px solid #ccc;
    box-shadow: 0px 3px 6px #ccc;
}

.turn {
    margin-top: 20px;
    margin-bottom: 20px;
    font-weight: bold;
    font-size: 22px;
}

.log ul {
    list-style: none;
    font-weight: bold;
    text-transform: uppercase;
}

.log ul li {
    margin: 5px;
}

.log ul .player-turn {
    color: blue;
    background-color: #e4e8ff;
}

.log ul .monster-turn {
    color: red;
    background-color: #ffc0c1;
}

button {
    font-size: 3rem;
    background-color: #eee;
    padding: 12px;
    box-shadow: 0 1px 1px black;
    margin: 10px;
}

#start-game {
    background-color: #aaffb0;
}

#start-game:hover {
    background-color: #76ff7e;
}

#attack {
    background-color: #ff7367;
}

#attack:hover {
    background-color: #ff3f43;
}

#special-attack {
    background-color: #ffaf4f;
}

#special-attack:hover {
    background-color: #ff9a2b;
}

#heal {
    background-color: #aaffb0;
}

#heal:hover {
    background-color: #76ff7e;
}

#give-up {
    background-color: #ffffff;
}

#give-up:hover {
    background-color: #c7c7c7;
}
.poke-img {
  height: 300px;
  border: solid red;
}
</style>
