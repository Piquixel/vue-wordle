<script>
import ResultPopout from "./ResultPopout.vue";
import GameBoard from "./GameBoard.vue";
import DarkModeInput from "./DarkModeInput.vue";

export default {
  components: { ResultPopout, GameBoard, DarkModeInput },

  data() {
    return {
      gameFinished: false,
      apiCall: "https://trouve-mot.fr/api/size/",
      darkMode: true,
      wordToGuess: "",
      keyboard: [],
      attempts: new Array(this.nbAttempts),
      nbAttempts: 6,
      wordLength: 5,
    };
  },

  methods: {
    //fonction qui vérifie si le mot a déjà été tiré et qui le tire dans le cas échéant
    setWord() {
      if (!localStorage.getItem("wordToGuess")) {
        fetch(this.apiCall + this.wordLength)
          .then((response) => {
            if (!response.ok) {
              throw new Error("Word not found");
            }
            return response.json();
          })
          .then((data) => {
            console.log(data);
            this.wordToGuess = data[0]["name"];
            localStorage.setItem("wordToGuess", this.wordToGuess);
          })
          .catch((error) => {
            console.log(error.message);
          });
      } else {
        this.wordToGuess = localStorage.getItem("wordToGuess");
      }
    },

    //fonction qui vérifie si un clavier est déjà en localstorage, et dans le cas échéant le créer dans un état par défaut
    setKeyboard() {
      if (!localStorage.getItem(this.wordToGuess)) {
        this.keyboard = [
          // Ligne 1
          { key: "A", status: "unused" },
          { key: "Z", status: "unused" },
          { key: "E", status: "unused" },
          { key: "R", status: "unused" },
          { key: "T", status: "unused" },
          { key: "Y", status: "unused" },
          { key: "U", status: "unused" },
          { key: "I", status: "unused" },
          { key: "O", status: "unused" },
          { key: "P", status: "unused" },

          // Ligne 2
          { key: "Q", status: "unused" },
          { key: "S", status: "unused" },
          { key: "D", status: "unused" },
          { key: "F", status: "unused" },
          { key: "G", status: "unused" },
          { key: "H", status: "unused" },
          { key: "J", status: "unused" },
          { key: "K", status: "unused" },
          { key: "L", status: "unused" },
          { key: "M", status: "unused" },

          // Ligne 3
          { key: "ENTER", status: "unused" },
          { key: "W", status: "unused" },
          { key: "X", status: "unused" },
          { key: "C", status: "unused" },
          { key: "V", status: "unused" },
          { key: "B", status: "unused" },
          { key: "N", status: "unused" },
          { key: "DEL", status: "unused" },
        ];
      } else {
        this.keyboard = JSON.parse(localStorage.getItem("keyboard"));
      }
      localStorage.setItem("keyboard", JSON.stringify(this.keyboard));
    },

    //fonction qui vérifie si des tentatives sont déjà présentes
    setAttempts() {
      if (localStorage.getItem("attempts")) {
        this.attempts = localStorage.getItem("attempts");
      }
    },

    setDarkMode() {
      if (localStorage.getItem("darkMode") === null) {
        localStorage.setItem("darkMode", true);
      } else {
        this.darkMode = JSON.parse(localStorage.getItem("darkMode"));
      }

      if (this.darkMode === true) {
        document.body.classList.remove("light-mode");
      } else {
        document.body.classList.add("light-mode");
      }
    },

    toggleDark() {
      var bdy = document.body;
      this.darkMode = !this.darkMode;
      localStorage.setItem("darkMode", JSON.stringify(this.darkMode));
      bdy.classList.toggle("light-mode");
    },
  },

  beforeMount() {
    //lance des fonctions au chargement de la page
    this.setWord();
    this.setKeyboard();
    this.setAttempts();
    this.setDarkMode();
  },
};
</script>

<template>
  <DarkModeInput @toggleDark="toggleDark" :darkMode="darkMode"></DarkModeInput>
  <GameBoard :wordToGuess="wordToGuess" :attempts="attempts" :keyboard="keyboard" :nbAttempts="nbAttempts" :wordLength="wordLength"></GameBoard>
  <ResultPopout v-if="gameFinished"></ResultPopout>
</template>

<style>
:root {
  --dk-bk-color: #2e2e2e;
  --dk-sec-color: #69e169;
}
body {
  background-color: var(--dk-bk-color);
  color: white;
  transition: 0.8s cubic-bezier(0.11, 0, 0.5, 0);
}
.light-mode {
  background-color: aliceblue;
  color: black;
}
</style>
