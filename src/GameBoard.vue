<script>
import WordGrid from "@/WordGrid.vue";
import KeyInput from "@/KeyInput.vue";
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
    //méthodes pour remonter les événements des composants enfants
    sendKeyInput(keyValue) {
      this.keyInput = keyValue;

      this.$nextTick(() => {
        this.keyInput = "";
      });
    },
  },

  emits: ["attemptsUpdate", "lettersState"],
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
  ></WordGrid>
  <KeyInput :keysState="keysState" @keyValue="sendKeyInput"></KeyInput>
</template>

<style></style>
