<script>
import WordGrid from "./WordGrid.vue";
import KeyInput from "./KeyInput.vue";
export default {
  components: { KeyInput, WordGrid },

  props: {
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
    wordToGuess: {
      type: String,
      required: true,
    },
  },

  data() {
    return {
      keyInput: "",
    };
  },

  methods: {
    //méthodes pour remonter l'état des lettres et des tentatives au composant parent
    updateLettersState(lettersState) {
      this.$emit("updateLettersState", lettersState);
    },

    updateAttempts(attempts) {
      this.$emit("updateAttempts", attempts);
    },

    //méthode pour recevoir la touche appuyée depuis le composant KeyInput
    sendKeyInput(keyValue) {
      this.keyInput = keyValue;
      console.log(this.keyInput);

      this.$nextTick(() => {
        this.keyInput = "";
      });
    },
  },

  emits: ["updateLettersState", "updateAttempts"],
};
</script>

<template>
  <WordGrid
    id="wordGrid"
    :wordToGuess="wordToGuess"
    :attempts="attempts"
    :keyInput="keyInput"
    :keysState="keysState"
    :nbAttempts="nbAttempts"
    @lettersState="updateLettersState"
    @attemptsUpdate="updateAttempts"
  ></WordGrid>
  <KeyInput :keysState="keysState" @keyValue="sendKeyInput"></KeyInput>
</template>

<style></style>
