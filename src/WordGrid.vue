<script>
import WordLine from "./WordLine.vue";
export default {
  components: { WordLine },
  props: [
    "wordToGuess", // mot à deviner
    "attempts", // liste des tentatives
    "nbAttempts", // nombre de tentatives maximales
    "keyInput", // valeur saisie par l'utilisateur
  ],
  methods: {
    // méthodes pour remonter les événements des composants enfants
    handleGuess({ lettersState, word }) {
      // insertion du mot dans la liste des tentatives
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
