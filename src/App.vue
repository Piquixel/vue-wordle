<script>
import ResultPopout from "./ResultPopout.vue";
import GameBoard from "./GameBoard.vue";
import DarkModeInput from "./DarkModeInput.vue";

export default {
  components: { ResultPopout, GameBoard, DarkModeInput },

  data() {
    return {
      gameFinished: false, // à set en localStorage !!
      apiCall: "https://trouve-mot.fr/api/size/",
      darkMode: true,
      wordToGuess: "",
      keysState: [],
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

    //fonction qui vérifie si l'état du clavier est déjà en localstorage, et dans le cas échéant le créer dans un état par défaut
    setkeysState() {
      if (!localStorage.getItem("keysState")) {
        this.keysState = [
          // Ligne 1
          { key: "Z", status: null },
          { key: "A", status: null },
          { key: "E", status: null },
          { key: "R", status: null },
          { key: "T", status: null },
          { key: "Y", status: null },
          { key: "U", status: null },
          { key: "I", status: null },
          { key: "O", status: null },
          { key: "P", status: null },

          // Ligne 2
          { key: "Q", status: null },
          { key: "S", status: null },
          { key: "D", status: null },
          { key: "F", status: null },
          { key: "G", status: null },
          { key: "H", status: null },
          { key: "J", status: null },
          { key: "K", status: null },
          { key: "L", status: null },
          { key: "M", status: null },

          // Ligne 3
          { key: "ENTER", status: null },
          { key: "W", status: null },
          { key: "X", status: null },
          { key: "C", status: null },
          { key: "V", status: null },
          { key: "B", status: null },
          { key: "N", status: null },
          { key: "DEL", status: null },
        ];
      } else {
        this.keysState = JSON.parse(localStorage.getItem("keysState"));
      }
      localStorage.setItem("keysState", JSON.stringify(this.keysState));
    },

    //fonction qui vérifie si des tentatives sont déjà présentes
    setAttempts() {
      if (localStorage.getItem("attempts")) {
        this.attempts = JSON.parse(localStorage.getItem("attempts"));
      }
      localStorage.setItem("attempts", JSON.stringify(this.attempts));
    },

    //fonction qui vérifie le mode sombre au chargement
    setDarkMode() {
      var dark = document.getElementsByClassName('dark-mode')[0];
      if (localStorage.getItem("darkMode") === null) {
        localStorage.setItem("darkMode", true);
        dark.classList.add("active");
        this.darkMode = true;
      } else {
        this.darkMode = JSON.parse(localStorage.getItem("darkMode"));
      }

      if (this.darkMode === true) {
        dark.classList.add("active");
      } else {
        dark.classList.remove("active");
      }
    },

    //fonction qui change le mode sombre
    toggleDark() {
      // var bdy = document.body;
      // this.darkMode = !this.darkMode;
      // localStorage.setItem("darkMode", JSON.stringify(this.darkMode));
      // bdy.classList.toggle("light-mode");
      let dark = document.getElementsByClassName('dark-mode')[0];

      this.darkMode = !this.darkMode;
      localStorage.setItem("darkMode", JSON.stringify(this.darkMode));
      dark.classList.toggle("active");
    },
  },

  beforeMount() {
    //lance des fonctions au chargement de la page
    this.setWord();
    this.setkeysState();
    this.setAttempts();
  },

  mounted() {
    this.setDarkMode(); //lance après que les éléments soient montés dans le DOM
  }
};
</script>

<template>
  <DarkModeInput id="darkModeInput" @toggleDark="toggleDark" :darkMode="darkMode"></DarkModeInput>
  <GameBoard id="GameBoard" :wordToGuess="wordToGuess" :attempts="attempts" :keysState="keysState" :nbAttempts="nbAttempts"></GameBoard>
  <ResultPopout id="ResultPopout" v-if="gameFinished" :attempts="attempts"></ResultPopout>
</template>

<style>

</style>
