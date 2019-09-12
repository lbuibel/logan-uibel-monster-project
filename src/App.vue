<template>

  <div id="app">


    <section v-if="!gameIsRunning">
      <div id="decisionCard">
        <v-card
        width = "50%"
        >
        </v-card>
        </div>

    </section>
    <div class="container">

    <!------------ Monster Card ------------>
    <div id="monsterCard">
      <v-card
        max-width = "40vw"
      >
      <v-card-title> {{ monsterName }}</v-card-title>
      <v-img class="cardImage"
      height = "auto"
      width = "50%"
      v-bind:src = "image1"
      ></v-img>
      </v-card>

        <v-progress-linear
          background-color="#ffc0c1"
          color="#1697F6"
          height="20"
          v-model= monsterHealth
          striped
          rounded
          reactive
          ></v-progress-linear>
          HP {{ playerHealth }}
    </div>

    <!------------ Player Card ------------>

    <div id="playerCard">
      <v-card
        max-width = "40vw"
      >
      <v-card-title> {{ playerName }}</v-card-title>
      <v-img class="cardImage"
      height = "auto"
      width = "50%"
      v-bind:src = "image2"
      ></v-img>

      </v-card>

        <v-progress-linear
          background-color="#ffc0c1"
          color="#1697F6"
          height="20"
          v-model= monsterHealth
          striped
          rounded
          reactive
          ></v-progress-linear>
          HP {{ monsterHealth }}
    </div>

    </div> <!-- Container Ends -->


<!--
    <div id="oponent">
      <div id=oponentInfo>
        <h1 class="text-left"> {{ monsterName }}</h1>
        <div class="healthbar">
        <div>
              <v-progress-linear
                background-color="#ffc0c1"
                color="#1697F6"
                height="20"
                v-model= playerHealth
                striped
                rounded
                reactive
              ></v-progress-linear>
              HP {{ playerHealth }}
          </div>
        </div>
      </div>
      <div class="oponent-image">
          <img v-bind:src = "image1" alt="none">
      </div>
    </div>

  <div>
    <div id="oponent">
        <div class="player-image" id="app3">
          <img v-bind:src = "image2" alt="none">
        </div>
      <div id="playerInfo">
        <h1 class="text-right"> {{playerName}}</h1>
        <div class="healthbar">
          <div>
              <v-progress-linear
                background-color="#ffc0c1"
                color="#1697F6"
                height="20"
                v-model= monsterHealth
                striped
                rounded
                reactive
              ></v-progress-linear>
              HP {{ monsterHealth }}
          </div>
        </div>
      </div>
    </div>
  </div>
-->
  <section v-if="!gameIsRunning">
    <div class="player-input">
      <div id="battle-alert">
     <h1>Wild {{ monsterName }} appeared!</h1>
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

      <v-col>
      <div class="my-2">
        <v-btn large color="red.base" @click="specialAttack">Text Button</v-btn>
      </div>
      </v-col>

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
//import HelloWorld from './components/HelloWorld.vue'
import { pokemon } from './assets/pokedex'
import colors from 'vuetify/lib/util/colors'

import {VApp, VBtn, VCol} from 'vuetify/lib'

console.log(colors)

let player1 = Math.floor(Math.random() * 151) + 0;
let player2 = Math.floor(Math.random() * 151) + 0;

let randomImage = `images/${player1 + 1}.png`;
let randomImage2 = `images/${player2 + 1}.png`;


let hp = pokemon[player1].base.HP;
let attack = pokemon[player1].base.Attack;

console.log(`${pokemon[player1].name.english} HP: ${hp}`)
console.log(`${pokemon[player1].name.english} HP: ${attack}`)
console.log(randomImage)


export default {
  name: 'app',
  components: {
    VApp,
    VBtn,
    VCol,
  },
  data: function () {
    return {
        playerName: pokemon[player1].name.english,
        monsterName: pokemon[player2].name.english,
        playerHealth: 100,
        monsterHealth: 100,
        gameIsRunning: false,
        turns: [],
        image1: randomImage2,
        image2: randomImage,
    }
    },
    methods: {
        startGame: function () {
            this.gameIsRunning = true;
            this.playerHealth = 100;
            this.monsterHealth = 100;
            this.turns = [];
        },
        fight: function () {
          this.gameIsRunning = true;
        },
        attack: function () {
            var damage = this.calculateDamage(3, 10);
            this.monsterHealth -= damage;
            this.turns.unshift({
                isPlayer: true,
                text: 'Player hits Monster for ' + damage
            });
            if (this.checkWin()) {
                return;
            }

            this.monsterAttacks();
        },
       specialAttack: function () {
            var damage = this.calculateDamage(10, 20);
            this.monsterHealth -= damage;
            console.log("monster health is " + this.monsterHealth)
            this.turns.unshift({
                isPlayer: true,
                text: 'Player hits Monster hard for ' + damage
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
                text: 'Player heals for 10'
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
                text: 'Monster hits Player for ' + damage
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
        },
    }
}


</script>

<style>
.container {
  width: 80vw;
  margin-left: auto;
  margin-right: auto;
  display: flex;
  justify-content: center;
}

#monsterCard {
  width: 45%;
  margin-right: 2%;
  margin-bottom: 1%;
}
#playerCard {
  width: 45%;
    margin-bottom: 1%;
}

.cardImage {
  margin-left: auto;
  margin-right: auto;
}




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
  height: 175px;
  width: 40%;
  float: right;
  margin-right: 5%;
}
img {
  height: 175px;
  width: auto;
}

.player-image {
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
