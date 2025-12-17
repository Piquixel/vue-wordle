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
      keyboard:{
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
    },

    data(){
      return {

      }
    }
  }
</script>

<template>
  <WordGrid :attempts="attempts" :wordLength="wordLength" :nbAttempts="nbAttempts"></WordGrid>
  <KeyInput :keyboard="keyboard"></KeyInput>
</template>

<style>
</style>
