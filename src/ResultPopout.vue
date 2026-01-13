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
    <span v-if="lostCheck == true">
      <h1>Perdu !</h1>
      <p>Le mot était : {{ wordToGuess }}</p>
    </span>

    <span v-else>
      <h1>Gagné !</h1>
    </span>

    <button id="replayBtn" @click=replay>Rejouer !</button>
  </div>
</template>
