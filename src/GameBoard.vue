<script>
  import WordGrid from "./WordGrid.vue";
  import KeyInput from "./KeyInput.vue";
  export default {
    components: { KeyInput, WordGrid},

    props:{
      attempts:{
        type: Array,
        required: true
      },
      keysState:{
        type: Array,
        required: true
      },
      nbAttempts:{
        type: Number,
        required: true
      },
      // wordLength:{
      //   type: Number,
      //   required: true
      // },
      wordToGuess:{
        type: String,
        required: true
      },
    },

    data(){
      return {
        keyInput : '',
      }
    },

    methods: {
      //méthodes pour remonter les événements des composants enfants
      updateLettersState(lettersState){
        this.$emit('updateLettersState', lettersState);
      },

      sendKeyInput(keyValue){
        if (keyValue != ''){
          this.keyInput = keyValue;
        }
      },

      updateAttempts(attempts){
        this.$emit('updateAttempts', attempts);
      },
    }
  }
</script>

<template>
  <WordGrid id="wordGrid" :wordToGuess="wordToGuess" :attempts="attempts" :keyInput="keyInput" :keysState="keysState" :nbAttempts="nbAttempts" @lettersState="updateLettersState" @attempts="updateAttempts"></WordGrid>
  <KeyInput id="keyInput" :keysState="keysState" @keyValue="sendKeyInput"></KeyInput>
</template>

<style>

</style>
