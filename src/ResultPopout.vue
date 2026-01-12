<script>
export default {
  props: {
    attempts: {
      type: Array,
      required: true
    },
    wordToGuess: {
      type: String,
      required: true
    }
  },

  data() {
    return {
    }
  },

  computed: {
    lostCheck(){
      let result = true;
      this.attempts.forEach(attempt => {
        if (attempt == this.wordToGuess){
          result = false;
        }
      });
      return result;
    },
  },

  methods:{
    replay(){
      this.$emit('replay');
    },
  }
};
</script>

<template>
  <div id="resultPopout-box">
    <h1 v-if="lostCheck == true">Perdu !</h1>
    <h1 v-else>Gagné !</h1>

    <button id="replayBtn" @click=replay>Rejouer !</button>
  </div>
</template>
