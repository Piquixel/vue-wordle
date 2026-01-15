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
      currentGuess: Array(this.wordToGuess.length).fill(""),
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
        console.log(this.cursor);
        if (this.cursor > 0) {
          this.cursor--;
          this.currentGuess[this.cursor] = "";
        }
      } else if (this.cursor < this.wordToGuess.length && key.length === 1) {
        this.currentGuess[this.cursor] = key.toUpperCase();
        this.cursor++;
      }
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
          result[letter] = result[letter] === "correct" ? "correct" : "unordered";
        } else {
          result[letter] = result[letter] || "wrong";
        }
      });

      this.$emit("submitGuess", { letterState: result, word: guessStr });
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
