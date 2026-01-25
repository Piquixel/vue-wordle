<script>
import ResultPopout from "@/ResultPopout.vue";
import GameBoard from "@/GameBoard.vue";
import DarkModeInput from "@/DarkModeInput.vue";

const KEY_UUID = "46e3f3a5-6a3c-4b4b-9b9b-6a3c4b4b9b9b";

export default {
  components: { ResultPopout, GameBoard, DarkModeInput },

  data() {
    return {
      gameFinished: false, // à set en localStorage !!
      darkMode: "",
      wordToGuess: "",
      keysState: [
        // Ligne 1
        { key: "A" },
        { key: "Z" },
        { key: "E" },
        { key: "R" },
        { key: "T" },
        { key: "Y" },
        { key: "U" },
        { key: "I" },
        { key: "O" },
        { key: "P" },

        // Ligne 2
        { key: "Q" },
        { key: "S" },
        { key: "D" },
        { key: "F" },
        { key: "G" },
        { key: "H" },
        { key: "J" },
        { key: "K" },
        { key: "L" },
        { key: "M" },

        // Ligne 3
        { key: "ENTER", status: "wide" },
        { key: "W" },
        { key: "X" },
        { key: "C" },
        { key: "V" },
        { key: "B" },
        { key: "N" },
        { key: "DEL", status: "wide" },
      ],
      attempts: [],
      nbAttempts: 6,
      wordLength: 5,
    };
  },

  methods: {
    /**
     * fonction qui permet de charger des données depuis le localstorage
     * @param {string} key - clef de recherche
     * @returns {any}
     */
    loadData(key) {
      const item = localStorage.getItem(key);
      if (!item) return null;
      if (item.startsWith("[") && item.endsWith("]")) {
        return JSON.parse(item);
      }
      if (item === "true" || item === "false") return item === "true";
      return item;
    },

    /**
     * fonction qui permet de sauvegarder des données dans le localstorage
     * @param {string} key - clef de recherche
     * @param {any} data
     * @returns {void}
     */
    saveData(key, data) {
      localStorage.setItem(key, Array.isArray(data) ? JSON.stringify(data) : data);
    },

    /**
     * fonction qui "encode" ou plus exactement brouille le mot à deviner pour éviter qu'il soit visible dans le localstorage
     * @param {string} word
     * @returns {string}
     */
    encodeWord(word) {
      const key = KEY_UUID;

      const mixed = word
        .split("")
        .map((char, i) => String.fromCharCode(char.charCodeAt(0) ^ key.charCodeAt(i % key.length)))
        .join("");

      return btoa(mixed);
    },

    /**
     * fonction qui "décode" le mot contenu dans le localstorage
     * @param {string} encoded
     * @returns {string}
     */
    decodeWord(encoded) {
      const key = KEY_UUID;
      const mixed = atob(encoded);

      return mixed
        .split("")
        .map((char, i) => String.fromCharCode(char.charCodeAt(0) ^ key.charCodeAt(i % key.length)))
        .join("");
    },

    /**
     * fonction qui permet de normaliser le texte et de supprimer les diacritiques
     * @param {string} str
     * @returns {string}
     */
    normalizeText(str) {
      return str
        .normalize("NFD")
        .replace(/\p{Diacritic}/gu, "")
        .toUpperCase();
    },

    /**
     * fonction qui vérifie si le mot a déjà été tiré et qui le tire dans le cas échéant
     */
    setWord() {
      const storedWord = this.loadData("wordToGuess");
      if (!storedWord) {
        fetch(`https://trouve-mot.fr/api/size/${this.wordLength}`)
          .then((res) => {
            if (!res.ok) throw new Error("Word not found");
            return res.json();
          })
          .then((data) => {
            this.wordToGuess = this.normalizeText(data[0]["name"]);
            if (this.wordToGuess.includes("Œ")) {
              window.location.reload();
            }
            this.saveData("wordToGuess", this.encodeWord(this.wordToGuess));
          })
          .catch((err) => console.error(err));
      } else {
        this.wordToGuess = this.decodeWord(storedWord);
      }
    },

    loadKeysState() {
      const storedKeysState = this.loadData("keysState");
      if (storedKeysState) {
        this.keysState = storedKeysState;
      }
    },

    //fonction qui vérifie si l'état du clavier est déjà en localstorage, et dans le cas échéant le créer dans un état par défaut
    setkeysState() {
      const storedKeysState = this.loadData("keysState");
      if (storedKeysState) {
        storedKeysState.forEach((storedKey) => {
          this.keysState.find((char) => char.key === storedKey.key).status = storedKey.status;
        });
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
      const isDark = this.loadData("darkMode");
      if (isDark === null) {
        this.darkMode = true;
        this.saveData("darkMode", this.darkMode);
        document.documentElement.style.colorScheme = "dark";
      } else {
        this.darkMode = !isDark;
        this.toggleDark();
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
    toggleDark({ x, y } = {}) {
      if (x && y) {
        const overlay = document.createElement("div");
        overlay.className = "theme-transition";
        overlay.style.setProperty("--x", `${x}px`);
        overlay.style.setProperty("--y", `${y}px`);

        document.body.appendChild(overlay);
        requestAnimationFrame(() => overlay.classList.add("active"));

        overlay.addEventListener("transitionend", () => overlay.remove());
      }

      this.darkMode = !this.darkMode;
      this.saveData("darkMode", this.darkMode);
      // document.documentElement.classList.toggle("dark", this.darkMode);
      document.documentElement.style.colorScheme = this.darkMode ? "dark" : "light";
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
  <header>
    <h1 id="title">Motus</h1>
    <div class="btns">
      <button class="material-symbols-rounded">settings</button>
      <DarkModeInput @toggleDark="toggleDark" :darkMode="darkMode">{{
        darkMode ? "dark_mode" : "light_mode"
      }}</DarkModeInput>
    </div>
  </header>
  <main>
    <GameBoard
      :word-to-guess="wordToGuess"
      :attempts="attempts"
      :keys-state="keysState"
      :nb-attempts="nbAttempts"
      @attemptsUpdate="updateAttempts"
      @lettersState="updateLettersState"
    ></GameBoard>
    <ResultPopout
      v-if="gameFinished"
      @replay="replay"
      :word-to-guess="wordToGuess"
      :attempts="attempts"
    ></ResultPopout>
  </main>
</template>
