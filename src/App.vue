<template>
<div>
  <div class="app">
    <header>
      <div class="flex-wrap">
        <h1 class="item"><img src="../src/assets/pokeball.png" alt="" width="30px"> PokeGuess</h1>
        <div class="score item">
            Score: {{ score }}
        </div>
      </div>
    </header>
    <div class="flex flex-wrap">
      <div class="item">
        <img :src="pokemon.sprites.front_default" alt="" width="400px">
      </div>
      <div class="item" v-show="!lost">
        <ul class="answers">
          <li v-for="answer in answers" :key="answer">I choose you, <button @click="guess(answer)">{{ pokemon_list[answer].name['english'] }}</button>!</li>
        </ul>
      </div>
       <div class="item lost" v-show="lost">
          {{ pokemon.name.toUpperCase() }}
          <button @click="init">Try Again</button>
        </div>
    </div>
  </div>
  <footer>
    Made with <a href="https://pokeapi.co/"><img src="../src/assets/pokeball.png" alt="" width="20px"></a> by <a href="https://essex.dev">Lewis Rhys Hill</a>.
  </footer>
  </div>
</template>

<script>
  import pokemon_list from './pokemon.json';
  export default {
    data() {
      return {
        pokemon_limit: 151, // pokemon_list.length,
        pokemon_id: 1,
        pokemon: null,
        answers: [],
        pokemon_list: pokemon_list,
        score: 0,
        lost: false
      }
    },
    methods: {
      init: function() {
        this.lost = false;
        this.score = 0;
        this.nextRound();
      },
      guess: function(answer) {
        if (this.pokemon.id - 1 == answer) {
          this.score++;
          this.nextRound();
        } else {
          this.lost = true;
        }
      },
      nextRound: function() {
        const rand = Math.floor(Math.random() * this.pokemon_limit);
        this.fetchPokemon(rand);
      },
      fetchPokemon: async function(id = 100) {
        this.lost = false;
        const response = await fetch("https://pokeapi.co/api/v2/pokemon/" + id)
        const pokemon = await response.json()
        this.pokemon = pokemon
        this.answers = [];
        this.answers.push(pokemon.id - 1)

        while (this.answers.length < 4) {
          const rand = Math.floor(Math.random() * this.pokemon_limit);
          if (!this.answers.includes(rand)) {
            this.answers.push(rand);
          }
        }

        this.answers = this.answers.sort(() => Math.random() - 0.5)
      }
    },
    async created () {
      const response = await fetch("https://pokeapi.co/api/v2/pokemon/" + this.pokemon_id)
      const pokemon = await response.json()
      this.pokemon = pokemon

      this.answers.push(pokemon.id - 1)

      while (this.answers.length < 4) {
        const rand = Math.floor(Math.random() * this.pokemon_limit);
        if (!this.answers.includes(rand)) {
          this.answers.push(rand);
        }
      }

      this.answers = this.answers.sort(() => Math.random() - 0.5)

    },
  }
</script>

<style>
  body, html {
    margin: 0;
    padding: 0;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    text-align: center;
  }
  .app {
    max-width: 700px;
    margin: 0 auto;
  }

  .answers {
    font-size: 1.4em;
  }

  button {
    background: none;
    border: none;
      font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    font-size: 1em;
    cursor: pointer;
    border-bottom: 5px solid white;
    padding: 0px;
    color: #ff2600;
    text-decoration: underline;
  }

  .item {
    flex: 1;
    /* text-align: center; */
  }

  ul {
    list-style-type: none;
    margin-left: 0;
    padding-left: 0;
  }

  ul li {
    margin-bottom: 20px;

  }

  .score {
    font-size: 2em;
  }

  footer {
    border-top: black solid 20px;
    border-bottom: black solid 20px;
    background: #ff2600;
    padding: 40px;
    text-align: center;
  }

  footer a {
    color: white;
  }

  @media screen and (min-width: 420px) {
    .flex {
      display: flex;
      align-items: center;
    }
    .flex-wrap {
      flex-wrap: wrap;
    }
  }
</style>
