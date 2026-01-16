<script>
import WordCell from "./WordCell.vue";
export default {
  components: { WordCell },
  props: {
    // mot à deviner
    wordToGuess: {
      type: String,
      required: true,
    },
    // est-ce que la ligne actuelle est active
    active: {
      type: Boolean,
      default: false,
    },
    // la valeur saisie par l'utilisateur
    userInput: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      currentGuess: [], // mot saisi par l'utilisateur
      cursor: 0, // position actuelle dans le mot
    };
  },
  watch: {
    // mise à jour lors de la saisie par l'utilisateur
    userInput(key) {
      // si la ligne n'est pas active, ou si la touche est vide, on sort
      if (!this.active || !key) return;
      // si l'on confirme le mot entré, on l'envoit pour validation
      if (key === "ENTER" && this.cursor === this.wordToGuess.length) this.validateWord();
      // si la touche est backspace et que le curseur n'est pas au début, on efface le dernier caractère
      else if (key === "DEL" && this.cursor > 0) {
        this.cursor--;
        this.currentGuess[this.cursor] = "";
      }
      // sinon, on ajoute la lettre au mot
      else if (this.cursor < this.wordToGuess.length && key.length === 1) {
        this.currentGuess[this.cursor] = key.toUpperCase();
        this.cursor++;
      }
    },

    // définition de la longueur du mot à deviner
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
    // validation du mot
    validateWord() {
      const result = {}; // dictionnaire de résultats
      const guessStr = this.currentGuess.join(""); // mot saisi
      const target = this.wordToGuess; // mot à deviner

      this.currentGuess.forEach((letter, index) => {
        // si la lettre est dans le mot à deviner et à la bonne position
        if (letter === target[index]) result[letter] = "correct";
        // si la lettre est dans le mot à deviner mais à une autre position
        else if (target.includes(letter))
          // en cas de double lettre, on priorise la bonne position
          result[letter] = result[letter] === "correct" ? "correct" : "misplaced";
        // sinon, la lettre n'est pas dans le mot à deviner
        else result[letter] = result[letter] || "wrong";
      });

      // envoi des données au parent
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
