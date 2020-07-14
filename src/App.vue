<template>
  <v-app id="inspire">
    <!-- <v-navigation-drawer v-model="drawer" app clipped>
      <v-list dense>
        <v-list-item link>
          <v-list-item-action>
            <v-icon>mdi-view-dashboard</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Dashboard</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item link>
          <v-list-item-action>
            <v-icon>mdi-cog</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Settings</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer> -->

    <!-- <v-app-bar app clipped-left>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Application</v-toolbar-title>
    </v-app-bar> -->

    <v-main>
      <v-container fluid>
        <h1 class="pokemonTitle">
          Pokemon Memory
        </h1>
        <v-row align="center" justify="center">
          <v-col>
            <v-row>
              
              <v-col>
                <v-form v-on:submit.prevent="onSubmit" v-if="gameStatus">
                  <v-row justify="end">
                    <v-col cols="10">
                      <v-text-field
                        label="Enter Pokemon"
                        outlined
                        dense
                        v-model="pokemonInput"
                        v-if="gameStatus"
                        v-on:keydown.enter="pokemonSubmit"
                        color="black"
                        background-color="red"
                        single-line
                        sm12
                      ></v-text-field>
                    </v-col>
                  </v-row>
                  <v-row justify="end">
                    <v-col cols="7">
                      <v-btn @click="pokemonSubmit">Submit</v-btn>
                    </v-col>
                    <v-col cols="3">
                      <v-btn @click="pokemonSubmit">Restart</v-btn>
                    </v-col>
                  </v-row>
                </v-form>
                <v-row v-if="!gameStatus" justify="end">
                  <v-col cols="7">
                    <v-btn @click="startGame" align-self="center">
                      START
                    </v-btn>
                  </v-col>
                </v-row>
              </v-col>
              <v-col cols="4">
                <v-row>
                  <v-col class="textColor textFont">
                    {{ this.totalTime }}
                  </v-col>
                </v-row>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
        <v-row justify="center">
          <v-col>
            <v-row justify="center" dense>
              <ul>
                <li
                  v-for="(todo, index) in totalPokemon"
                  :key="index"
                  :value="todo"
                  class="textColor"
                >
                  {{ todo }}
                </li>
              </ul>
            </v-row>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <v-footer app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
//import PokemonList from './components/PokemonList'
export default {
  name: "HelloWorld",
  components: {
    //PokemonList
  },
  props: {
    source: String,
  },
  data: () => ({
    drawer: null,
    totalPokemonCount: 20,
    totalPokemon: [],
    pokemonInput: "",
    gameStatus: false,

    timer: null,
    totalTime: 0,
    resetButton: false,
  }),
  created() {
    this.$vuetify.theme.dark = true;
  },
  methods: {
    totalNumberOfPokemon: async function() {
      console.log("HelloWorld");
      const pokemonBaseUri = "http://localhost:3000/pokemon/total";
      return await this.$http.get(pokemonBaseUri).then((res) => {
        console.log(res.data);
        return res.data;
      });
    },
    occupyInitialArray: async function(pokemonLength) {
      for (let i = 0; i < pokemonLength; i++) {
        this.$set(this.totalPokemon, i, "");
      }
      console.log(this.totalPokemon);
    },
    pokemonSubmit: async function() {
      const pokemonBaseUri = `http://localhost:3000/pokemon/${this.pokemonInput}`;

      await this.$http.get(pokemonBaseUri).then((res) => {
        console.log(res.data);
        const singlePokemon = res.data;
        this.$set(
          this.totalPokemon,
          singlePokemon.number - 1,
          singlePokemon.name
        );
        for (let i = 0; i < this.totalPokemon.length; i++) {
          //game parameters must be changed
          if (!this.totalPokemon[i]) {
            break;
          }
          if (i === this.totalPokemon.length - 1) {
            console.log("End of the game");
            this.gameStatus = false;
            this.stopTimer();
          }
        }
        console.log(this.totalPokemon);
        this.pokemonInput = "";
      });
      //this.pokemonInput = ""
    },
    startGame: async function() {
      const totalNumber = await this.totalNumberOfPokemon();
      await this.occupyInitialArray(totalNumber);
      this.gameStatus = true;
      this.startTimer();
      console.log("HelloWorld");
    },

    startTimer: function() {
      this.totalTime = 0;
      this.timer = setInterval(() => this.countdown(), 1000);
      this.resetButton = true;
    },
    stopTimer: function() {
      clearInterval(this.timer);
      this.timer = null;
      this.resetButton = true;
    },
    resetTimer: function() {
      this.totalTime = 0;
      clearInterval(this.timer);
      this.timer = null;
      this.resetButton = false;
    },
    padTime: function(time) {
      return (time < 10 ? "0" : "") + time;
    },
    countdown: function() {
      this.totalTime++;
      // console.log(this.totalTime);
    },
  },

  async mounted() {
    const totalNumber = await this.totalNumberOfPokemon();
    console.log(totalNumber);
    await this.occupyInitialArray(totalNumber);
  },
};
</script>

<style scoped>
ul li {
  display: inline-block;
  text-align: center;
}
ul {
  text-align:center
}
li {
  width: 130px;
  height: 25px;
  margin: 10px;
  border: 2px solid blue;
  text-align: center;
}
.v-application ul {
  padding-left: 0;
}
.v-main {
  background-color: #ffde00;
}
.pokemonTitle {
  text-align: center;
  color: black;
}
.textColor {
  color: #cc0000 !important;
}
.textFont {
  font-size: 26px;
}
</style>
