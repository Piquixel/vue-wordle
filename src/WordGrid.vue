<script>
import WordLine from "./WordLine.vue";
export default {
  components: { WordLine },
  props: ["wordToGuess", "attempts", "nbAttempts", "keyInput"],
  data() {
    return {};
  },
  methods: {
    handleGuess({ lettersState, word }) {
      const newAttempts = [...this.attempts, word];
      this.$emit("attemptsUpdate", newAttempts);
      this.$emit("lettersState", lettersState);
    },
  },
};
</script>

<template>
  <table>
    <tbody>
      <WordLine
        v-for="(n, index) in nbAttempts"
        :key="index"
        :word-to-guess="wordToGuess"
        :active="index === attempts.length"
        :user-input="keyInput"
        @submitGuess="handleGuess"
      ></WordLine>
    </tbody>
  </table>
</template>
