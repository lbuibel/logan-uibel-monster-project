<template>

  <div id="app">


    <div class="container">

    <!------------ Pokemon Card ------------>
    <div id="playerCard">
        <v-card
          max-width = "40vw"
          height = "300px"

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
            v-model= playerHealth
            striped
            rounded
            reactive
            ></v-progress-linear>
            HP {{ playerHealth }}
    </div>

    <div id="monsterCard">
      <v-card
        max-width = "40vw"
        height = "300px"
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
          HP {{ monsterHealth }}
    </div>
 
    </div> 
    <!-- Container Ends -->


    <!-- Start Battle Prompt -->
    <section v-if="!gameIsRunning">
      <v-banner
        width= "95%"
        class = "container">
        A wild {{ monsterName }} appeared!<br>What would you like to do?
        <template v-slot:actions>
          <v-btn large rounded color="#F44336" @click="fight">ATTACK!</v-btn>
          <v-btn large rounded color="000000" @click="reload">RUN</v-btn>
        </template>
      </v-banner>
    </section>

    <!-- Battle Buttons -->
    <section v-else>
      <v-btn large rounded color="#F44336" @click="attack">ATTACK</v-btn>
      <v-btn large rounded color="#FF9800" @click="specialAttack">SPECIAL ATTACK </v-btn>
      <v-btn large rounded color="#4CAF50" @click="heal">HEAL</v-btn>

    <!-- Dialog box for running -->
      <div class="text-center">
        <v-dialog
        v-model="dialog"
        width="500"
        >
        <template v-slot:activator="{ on }">
          <v-btn large rounded color="#fefefe" @click="giveUp" v-on="on">GIVE UP</v-btn>

        </template>

        <v-card>
        <v-card-title
          class="headline grey lighten-2"
          primary-title
          >
          You're about to quit
          </v-card-title>

          <v-card-text> You sure you want to go through with this?
          </v-card-text>

          <v-divider></v-divider>

          <v-card-actions>
            <div class="flex-grow-1"></div>
              <v-btn
              color="#03A9F4"
              outlined
              @click="reload"
            >
            Give Up
            </v-btn>
          </v-card-actions>
        </v-card>
       </v-dialog>
      </div>
    </section>

    <!-- Attack Summary -->
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

<script>
import { pokemon } from './assets/pokedex'
import colors from 'vuetify/lib/util/colors'
import {VApp, VBtn, VCol, VDialog} from 'vuetify/lib'


let player1 = Math.floor(Math.random() * 151) + 0;
let player2 = Math.floor(Math.random() * 151) + 0;

let randomImage = `images/${player1 + 1}.png`;
let randomImage2 = `images/${player2 + 1}.png`;

export default {
  name: 'app',
  components: {
    VApp,
    VBtn,
    VCol,
    VDialog
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
        stillPlaying: true,
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
            var monster = this.monsterName;
            var player = this.playerName;
            var damage = this.calculateDamage(3, 10);
            this.monsterHealth -= damage;
            this.turns.unshift({
                isPlayer: true,
                text: player + ' hits ' + monster + ' for ' + damage
            });
            if (this.checkWin()) {
                return;
            }

            this.monsterAttacks();
        },
       specialAttack: function () {
            var monster = this.monsterName;
            var player = this.playerName;
            var damage = this.calculateDamage(10, 20);
            this.monsterHealth -= damage;
            this.turns.unshift({
                isPlayer: true,
                text: player + ' hits ' + monster + ' hard for ' + damage
            });
            if (this.checkWin()) {
                return;
            }
            this.monsterAttacks();
        },
        heal: function () {
          var player = this.playerName;
            if (this.playerHealth <= 90) {
                this.playerHealth += 10;
            } else {
                this.playerHealth = 100;
            }
            this.turns.unshift({
                isPlayer: true,
                text: player + ' heals for 10'
            });
            this.monsterAttacks();
        },
        giveUp: function () {
            this.stillPlaying = false;
        },
        reload: function () {
          location.reload();
        },
        monsterAttacks: function() {
          var monster = this.monsterName;
          var player = this.playerName;
            var damage = this.calculateDamage(5, 12);
            this.playerHealth -= damage;
            this.checkWin();
            this.turns.unshift({
                isPlayer: false,
              text: monster + ' hits ' + player + ' for ' + damage
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
  font-size: 1.5rem;
}


#monsterCard {
  width: 50%;
  margin-left: 2%;
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
    padding: 12px;
    margin: 10px;
}


</style>
