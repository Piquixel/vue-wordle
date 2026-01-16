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
        if (attempt == this.wordToGuess.toUpperCase()){
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
  <div id="resultPopout">
    <div id="resultPopout-box">
      <div v-if="lostCheck == true">
        <h1>Perdu !</h1>
        <em>Le mot était : {{ wordToGuess }}</em>
      </div>

      <div v-else>
        <h1>Gagné !</h1>
        <em>Vous avez trouvé le mot {{ wordToGuess }} en {{ attempts.length }} tentatives</em>
      </div>

      <button id="replayBtn" @click="replay">Rejouer !</button>
    </div>
  </div>
</template>
