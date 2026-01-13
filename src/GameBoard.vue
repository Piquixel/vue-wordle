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

      }
    },

    methods: {
      updateLettersState(lettersState){
        this.$emit('lettersState', lettersState);
      },

      updateKeyValue(keyValue){
        this.$emit('keyValue', keyValue);
      },

      updateAttempts(attempts){
        this.$emit('attempts', attempts);
      },
    }
  }
</script>

<template>
  <WordGrid id="wordGrid" :wordToGuess="wordToGuess" :attempts="attempts" :keysState="keysState" :nbAttempts="nbAttempts" @lettersState="updateLettersState" @attempts="updateAttempts"></WordGrid>
  <KeyInput id="keyInput" :keysState="keysState" @keyValue="updateKeyValue"></KeyInput>
</template>

<style>

</style>
