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

    <v-app-bar app clipped-left>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Application</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container fluid>
        <v-row align="center" justify="center">
          <v-col>
            <v-row>
              <v-col sm="6" md="3">
                <v-text-field label="Enter Pokemon" outlined dense v-model="pokemonInput"></v-text-field>
                <v-btn @click="pokemonSubmit">Submit</v-btn>
              </v-col>
              <v-col>
              </v-col>
              <!-- <v-col>
                <v-btn @click="occupyInitialArray">Submit Pokemon</v-btn>
              </v-col> -->
            </v-row>
          </v-col>
        </v-row>
        <v-row align="top" justify="center" dense>
          <v-col>
          <ul>
            <li
              v-for="(todo, index) in totalPokemon"
              :key="index"
              :value="todo"
            >
              {{ todo }}
            </li>
          </ul>
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
    totalPokemon: ["Hello", "Hello1", "Hello3"],
    pokemonInput: ""
  }),
  created() {
    this.$vuetify.theme.dark = true;
  },
  methods: {
    totalNumberOfPokemon: async function() {
      console.log("HelloWorld");
      const pokemonBaseUri = "http://localhost:3000/pokemon/total";
      return await this.$http.get(pokemonBaseUri).then((res) => {
        console.log(res.data)
        return res.data
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
        const singlePokemon = res.data
        this.$set(this.totalPokemon, singlePokemon.number -1, singlePokemon.name);
        console.log(this.totalPokemon)
        this.pokemonInput = ""
      });
      //this.pokemonInput = ""
    }
  },
  async mounted() {
    const totalNumber = await this.totalNumberOfPokemon()
    console.log(totalNumber)
    await this.occupyInitialArray(totalNumber)
  },
};
</script>

<style scoped>
ul li {
  display: inline-block;
}
li {
  width: 200px;
  height: 25px;
  margin: 10px;
  border: 2px solid blue;
  text-align: center;
}
.v-application ul{
  padding-left:0;
}
</style>
