# logan-uibel-monster-project

## Changes
# 1
The first change I made was adding a folder with pokemon images and a file with pokemon data. This was done to make the game take place between two pokemon rather than just a player and a monster.  After importing the files into my App.vue file, I added on to my data function to utilize the pokemon names and pictures for the battle. The specific things I added to the data function were playerName, monsterName, image1, and image2.  In the attack and heal functions I used these names to display the pokemons name in the battle summary from max's example.

# 2
Another change I made was using vuetify to display the pokemon image and name within a card.  To do this I had to use v-card, v-image, and v-card title.  I had to style the cards a bit to get them the size I wanted and spaced out a bit so they looked right. I also had to change the styling of the image within the card so it was centered.

# 3
The next change I made was changing the health bar to a vuetify progress bar.  I changed the coloring so the background was colored red, rather than it just being blank like in Max's example.  Changing the health bar width was done by passing on the player or monster health into the v-model which was also different from Max's example.

# 4
The next change was using a vuetify banner to prompt the player into starting a battle or running. Selecting the attack button changes the section out using an if else statement to now having battle buttons, hitting the run button calls a function that reloads the page to have different pokemon.

# 5
The last big change I made was adding a dialog box for when the player 'gives up' on the battle, which contains a text prompt asking if they're sure they want to give up, and a button which will then reload the page.  Along with this dialog box I changed all the buttons out and used vuetify to style new ones.

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn run serve
```

### Compiles and minifies for production
```
yarn run build
```

### Run your tests
```
yarn run test
```

### Lints and fixes files
```
yarn run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
