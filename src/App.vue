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
      darkMode: "",
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

    //fonction qui normalise le texte (supprime les accents et met en majuscules)
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
        ];
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
      } else {
        this.darkMode = this.darkMode = localStorage.getItem("darkMode") === "true";
      }
      dark.classList.toggle("dark", this.darkMode);
    },

    //fonction qui vérifie si la partie est déjà finie
    setGameFinished() {
      if (!localStorage.getItem("gameFinished")) {
        this.gameFinished = false;
      } else {
        this.gameFinished = JSON.parse(localStorage.getItem("gameFinished"));
      }
      localStorage.setItem("gameFinished", JSON.stringify(this.gameFinished));
    },

    setGameSettings() {
      if (localStorage.getItem("wordLength")) {
        this.wordLength = parseInt(localStorage.getItem("wordLength"));
      }
      if (localStorage.getItem("nbAttempts")) {
        this.nbAttempts = parseInt(localStorage.getItem("nbAttempts"));
      }

      localStorage.setItem("wordLength", this.wordLength);
      localStorage.setItem("nbAttempts", this.nbAttempts);
    },

    //lance touts les fonctions de setup de la page
    pageSetup() {
      this.setGameSettings();
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
      localStorage.setItem("wordLength", this.wordLength);
      localStorage.setItem("nbAttempts", this.nbAttempts);

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

    changeSettings(lg_word, nb_attempts) {
      this.wordLength = parseInt(lg_word);
      this.nbAttempts = parseInt(nb_attempts);
      localStorage.setItem("wordLength", this.wordLength);
      localStorage.setItem("nbAttempts", this.nbAttempts);
      this.replay();
    },

    showForm() {
      const form = document.getElementById("settingsForm");
      form.classList.toggle("visible");
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

  <button class="show-setting" @click="showForm()">
    <svg id="ico-showSetting" viewBox="0 0 1920 1920" xmlns="http://www.w3.org/2000/svg">
      <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
      <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
      <g id="SVGRepo_iconCarrier">
        <path
          d="M1703.534 960c0-41.788-3.84-84.48-11.633-127.172l210.184-182.174-199.454-340.856-265.186 88.433c-66.974-55.567-143.323-99.389-223.85-128.415L1158.932 0h-397.78L706.49 269.704c-81.43 29.138-156.423 72.282-223.962 128.414l-265.073-88.32L18 650.654l210.184 182.174C220.39 875.52 216.55 918.212 216.55 960s3.84 84.48 11.633 127.172L18 1269.346l199.454 340.856 265.186-88.433c66.974 55.567 143.322 99.389 223.85 128.415L761.152 1920h397.779l54.663-269.704c81.318-29.138 156.424-72.282 223.963-128.414l265.073 88.433 199.454-340.856-210.184-182.174c7.793-42.805 11.633-85.497 11.633-127.285m-743.492 395.294c-217.976 0-395.294-177.318-395.294-395.294 0-217.976 177.318-395.294 395.294-395.294 217.977 0 395.294 177.318 395.294 395.294 0 217.976-177.317 395.294-395.294 395.294"
          fill-rule="evenodd"
        ></path>
      </g>
    </svg>
  </button>

  <form
    id="settingsForm"
    @submit="changeSettings($event.target.lg_word.value, $event.target.nb_attempts.value)"
  >
    <div>
      <label for="lg_word">Longueur du mot :</label>
      <select name="lg_word" id="lg_word" :value="wordLength">
        <option value="4">4</option>
        <option value="5">5</option>
        <option value="6">6</option>
        <option value="7">7</option>
        <option value="8">8</option>
      </select>
    </div>

    <div>
      <label for="nb_attempts">Nombre de tentatives :</label>
      <select name="nb_attempts" id="nb_attempts" :value="nbAttempts">
        <option value="5">5</option>
        <option value="6">6</option>
        <option value="7">7</option>
        <option value="8">8</option>
        <option value="9">9</option>
      </select>
    </div>

    <button type="submit">Appliquer</button>
  </form>

  <GameBoard
    :wordToGuess="wordToGuess"
    :attempts="attempts"
    :keysState="keysState"
    :nbAttempts="nbAttempts"
    @updateLettersState="updateLettersState"
    @updateAttempts="updateAttempts"
  ></GameBoard>
  <ResultPopout
    v-if="gameFinished"
    @replay="replay"
    :wordToGuess="wordToGuess"
    :attempts="attempts"
  ></ResultPopout>
</template>

<style></style>
