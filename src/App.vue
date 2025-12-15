<script>
import WordGrid from "./WordGrid.vue";
import KeyInput from "./KeyInput.vue";
import ResultPopout from "./ResultPopout.vue";
export default {
  components: { KeyInput, WordGrid, ResultPopout},

  data(){
    return {
      gameFinished : false,
      apiCall : 'https://trouve-mot.fr/api/size/5',
      darkMode : localStorage.getItem('darkMode'),
      wordToGuess: '',
    }
  },

  methods:{
    //fonction qui vérifie si le mot a déjà été tiré et qui le tire dans le cas échéant
    setWord(){
      if (!localStorage.getItem(this.wordToGuess)){
        fetch(this.apiCall)
        .then(response => {
            if (!response.ok){
                throw new Error('Word not found');
            }
            return response.json();
        })
        .then(data => {
            this.wordToGuess = data.name;
        })
        .catch(error => {
            console.log(error.message);
        });
      }
      else{
        this.wordToGuess = localStorage.getItem('wordToGuess');
      }
      localStorage.setItem('wordToGuess', this.wordToGuess);
    }
  },

  beforeMount(){
    this.setWord();
  }
};
</script>

<template>
  <WordGrid></WordGrid>
  <KeyInput></KeyInput>
  <ResultPopout v-if="gameFinished"></ResultPopout>
</template>
