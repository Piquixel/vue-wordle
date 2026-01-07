<script>
import WordLine from "./WordLine.vue";
export default {
  components: { WordLine },
  props: {
    wordToGuess: {
      type: String,
      required: true,
    },
    attempts: {
      type: Array,
      required: true,
    },
    keysState: {
      type: Array,
      required: true,
    },
    nbAttempts: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {};
  },
  methods: {},
};
</script>

<template>
  <table>
    <tbody>
      <WordLine :word-to-guess="wordToGuess" v-for="word in nbAttempts" :key="word"></WordLine>
    </tbody>
  </table>
</template>
