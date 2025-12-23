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
      wordLength:{
        type: Number,
        required: true
      },
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
    }
  }
</script>

<template>
  <WordGrid :wordToGuess="wordToGuess" :attempts="attempts" :keysState="keysState" :nbAttempts="nbAttempts"></WordGrid>
  <KeyInput :keysState="keysState"></KeyInput>
</template>

<style>
</style>
