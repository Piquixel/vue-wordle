<script>
export default {
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
    // liste des tentatives
    attempt: {
      type: String,
      default: "",
    },
    keysState: {
      type: Array,
      required: true,
    },
    lineResult: {
      type: Array,
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
        this.currentGuess[this.cursor].input = "";
      }
      // sinon, on ajoute la lettre au mot
      else if (this.cursor < this.wordToGuess.length && key.length === 1) {
        this.currentGuess[this.cursor].input = key;
        this.cursor++;
      }
    },

    // définition de la longueur du mot à deviner
    wordToGuess: {
      immediate: true,
      handler(newWord) {
        if (this.attempt !== "" && Array.isArray(this.lineResult)) {
          this.currentGuess = this.attempt.split("").map((letter) => ({
            input: letter,
            state: this.lineResult[this.attempt.indexOf(letter)],
          }));
        } else if (newWord && newWord.length > 0) {
          this.currentGuess = Array.from({ length: newWord.length }, () => ({
            input: "",
            state: null,
          }));
        }
      },
    },
  },
  methods: {
    // validation du mot
    validateWord() {
      const result = {}; // dictionnaire de résultats
      const lineResult = [];
      const guessLetters = this.currentGuess.map((letter) => letter.input);
      const guessStr = guessLetters.join(""); // mot saisi
      const target = this.wordToGuess; // mot à deviner

      this.currentGuess.forEach((letter, index) => {
        // si la lettre est dans le mot à deviner et à la bonne position
        if (letter.input === target[index]) {
          result[letter.input] = "correct";
          lineResult.push("correct");
        }
        // si la lettre est dans le mot à deviner mais à une autre position
        else if (target.includes(letter.input)) {
          result[letter.input] = result[letter.input] === "correct" ? "correct" : "misplaced"; // en cas de double lettre, on priorise la bonne position
          lineResult.push("misplaced");
        }

        // sinon, la lettre n'est pas dans le mot à deviner
        else {
          result[letter.input] = result[letter.input] || "wrong";
          lineResult.push("wrong");
        }
      });

      // mise à jour des cellules de la grille
      this.currentGuess.forEach((letter, index) => {
        letter.state = lineResult[index];
      });
      this.cursor = 0;
      // envoi des données au parent
      this.$emit("submitGuess", { lettersState: result, word: guessStr, lineResult });
    },
  },
};
</script>

<template>
  <tr>
    <td
      v-for="(letter, i) in currentGuess"
      :class="{
        'char--wrong': letter.state === 'wrong',
        'char--misplaced': letter.state === 'misplaced',
        'char--correct': letter.state === 'correct',
      }"
      :key="i"
    >
      {{ letter.input }}
    </td>
  </tr>
</template>
