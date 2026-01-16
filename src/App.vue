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
      darkMode: '',
      wordToGuess: "",
      keysState: [],
      attempts: [],
      nbAttempts: 6,
      wordLength: 5,
    };
  },

  methods: {
    //fonction qui "encode" ou plus exactement brouille le mot à deviner pour éviter qu'il soit visible dans le localstorage
    encodeWord(word) {
      const key = "wordle_clone_key";

      const mixed = word
        .split("")
        .map((char, i) => String.fromCharCode(char.charCodeAt(0) ^ key.charCodeAt(i % key.length)))
        .join("");

      return btoa(mixed);
    },

    //fonction qui "décode" le mot contenu dans le localstorage
    decodeWord(encoded) {
      const key = "wordle_clone_key";
      const mixed = atob(encoded);

      return mixed
        .split("")
        .map((char, i) => String.fromCharCode(char.charCodeAt(0) ^ key.charCodeAt(i % key.length)))
        .join("");
    },

    normalizeText(str) {
      return str
        .normalize("NFD")
        .replace(/\p{Diacritic}/gu, "")
        .toUpperCase();
    },

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
            this.wordToGuess = this.normalizeText(data[0]["name"]);
            if (this.wordToGuess.includes("Œ")) {
              window.location.reload();
            }
            localStorage.setItem("wordToGuess", this.encodeWord(this.wordToGuess));
          })
          .catch((error) => {
            console.log(error.message);
          });
      } else {
        this.wordToGuess = this.decodeWord(localStorage.getItem("wordToGuess"));
      }
    },

    //fonction qui vérifie si l'état du clavier est déjà en localstorage, et dans le cas échéant le créer dans un état par défaut
    setkeysState() {
      if (!localStorage.getItem("keysState")) {
        this.keysState = [
        // Ligne 1
        { key: "A", status: null },
        { key: "Z", status: null },
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
        { key: "ENTER", status: "wide" },
        { key: "W", status: null },
        { key: "X", status: null },
        { key: "C", status: null },
        { key: "V", status: null },
        { key: "B", status: null },
        { key: "N", status: null },
        { key: "DEL", status: "wide" },
      ]
      } else {
        this.keysState = JSON.parse(localStorage.getItem("keysState"));
      }
      localStorage.setItem("keysState", JSON.stringify(this.keysState));
    },

    //fonction qui vérifie si des tentatives sont déjà présentes
    setAttempts() {
      if (!localStorage.getItem("attempts")) {
        localStorage.setItem("attempts", JSON.stringify(this.attempts));
      } else {
        this.attempts = JSON.parse(localStorage.getItem("attempts"));
      }
    },

    //fonction qui vérifie le mode sombre au chargement
    setDarkMode() {
      var dark = document.documentElement;
      if (localStorage.getItem("darkMode") == null) {
        localStorage.setItem("darkMode", true);
        this.darkMode = true;
        dark.classList.toggle("dark", this.darkMode);
      } else {
        this.darkMode = this.darkMode = localStorage.getItem("darkMode") === "true";;
      }
    },

    setGameFinished() {
      if (!localStorage.getItem("gameFinished")) {
        this.gameFinished = false;
      } else {
        this.gameFinished = JSON.parse(localStorage.getItem("gameFinished"));
      }
      localStorage.setItem("gameFinished", JSON.stringify(this.gameFinished));
    },

    pageSetup() {
      this.setWord();
      this.setkeysState();
      this.setAttempts();
      this.setGameFinished();
    },

    //fonction qui change le mode sombre
    toggleDark({ x, y }) {
      const overlay = document.createElement("div");
      overlay.className = "theme-transition";
      overlay.style.setProperty("--x", `${x}px`);
      overlay.style.setProperty("--y", `${y}px`);

      document.body.appendChild(overlay);
      requestAnimationFrame(() => overlay.classList.add("active"));

      this.darkMode = !this.darkMode;
      localStorage.setItem("darkMode", this.darkMode);
      document.documentElement.classList.toggle("dark", this.darkMode);

      overlay.addEventListener("transitionend", () => overlay.remove());
    },

    //fonction qui relance une partie et qui reset le localStorage (sauf le mode sombre)
    replay() {
        localStorage.clear();
        localStorage.setItem("darkMode", this.darkMode);

        this.wordToGuess = "";
        this.attempts = [];
        this.gameFinished = false;

        this.setkeysState();
        this.setWord(); // seulement ce qui est nécessaire
    },

    // fonction qui met à jour le tableau d'objet keysState
    updateLettersState(lettersState) {
      for (const [key, value] of Object.entries(lettersState)) {
        const index = this.keysState.findIndex((el) => el.key === key);

        if (index !== -1 && this.keysState[index].status !== "correct") {
          this.keysState[index].status = value;
        }
      }
      localStorage.setItem("keysState", JSON.stringify(this.keysState));
    },

    updateAttempts(attempts) {
      this.attempts = attempts;
      if (
        this.attempts.length == this.nbAttempts ||
        this.attempts[attempts.length - 1] == this.wordToGuess.toUpperCase()
      ) {
        this.gameFinished = true;
        localStorage.setItem("gameFinished", JSON.stringify(this.gameFinished));
      }
      localStorage.setItem("attempts", JSON.stringify(this.attempts));
    },
  },

  beforeMount() {
    //lance des fonctions au chargement de la page
    this.pageSetup();
  },

  mounted() {
    this.setDarkMode(); //lance après que les éléments soient montés dans le DOM
  },
};
</script>

<template>
  <DarkModeInput @toggleDark="toggleDark" :darkMode="darkMode"></DarkModeInput>
  <GameBoard
    v-if="!gameFinished"
    :wordToGuess="wordToGuess"
    :attempts="attempts"
    :keysState="keysState"
    :nbAttempts="nbAttempts"
    @updateLettersState="updateLettersState"
    @updateAttempts="updateAttempts"
  ></GameBoard>
  <ResultPopout
    v-else
    @replay="replay"
    :wordToGuess="wordToGuess"
    :attempts="attempts"
  ></ResultPopout>
</template>

<style></style>
