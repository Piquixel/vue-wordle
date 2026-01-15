<script>
import WordCell from "./WordCell.vue";
export default {
  components: { WordCell },
  props: {
    wordToGuess: {
      type: String,
      required: true,
    },
    active: {
      type: Boolean,
      default: false,
    },
    userInput: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      currentGuess: [],
      cursor: 0,
    };
  },
  watch: {
    userInput(key) {
      if (!this.active || !key) return;
      if (key === "ENTER") {
        if (this.cursor === this.wordToGuess.length) {
          this.validateWord();
        }
      } else if (key === "DEL") {
        if (this.cursor > 0) {
          this.cursor--;
          this.currentGuess[this.cursor] = "";
        }
      } else if (this.cursor < this.wordToGuess.length && key.length === 1) {
        this.currentGuess[this.cursor] = key.toUpperCase();
        this.cursor++;
      }
    },
    wordToGuess: {
      immediate: true,
      handler(newWord) {
        if (newWord && newWord.length > 0) {
          this.currentGuess = Array(newWord.length).fill("");
        }
      },
    },
  },
  methods: {
    validateWord() {
      const result = {};
      const guessStr = this.currentGuess.join("");
      const target = this.wordToGuess.toUpperCase();

      this.currentGuess.forEach((letter, index) => {
        if (letter === target[index]) {
          result[letter] = "correct";
        } else if (target.includes(letter)) {
          result[letter] = result[letter] === "correct" ? "correct" : "misplaced";
        } else {
          result[letter] = result[letter] || "wrong";
        }
      });

      this.$emit("submitGuess", { lettersState: result, word: guessStr });
    },
  },
};
</script>

<template>
  <tr>
    <WordCell v-for="(letter, i) in currentGuess" :key="i">
      {{ letter }}
    </WordCell>
  </tr>
</template>
