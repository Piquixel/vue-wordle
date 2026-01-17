<script>
import KeyboardKey from "@/KeyboardKey.vue";
export default {
  components: { KeyboardKey },
  props: { keysState: Array },
  methods: {
    //remonter la valeur de la touche appuyée au composant parent GameBoard
    forwardKey(keyValue) {
      if (keyValue) this.$emit("KeyValue", keyValue);
    },
    //gestion du clavier physique
    physkey() {
      window.addEventListener("keyup", this.handlePhysicalKeyboard);
    },
    handlePhysicalKeyboard(event) {
      let letter = event.key
        .normalize("NFD")
        .replace(/\p{Diacritic}/gu, "")
        .toUpperCase();
      // Lettres A-Z
      if (/[A-Z]{1}/.test(letter)) {
        this.forwardKey(letter);
      }
      // ENTER
      if (letter === "ENTER") {
        this.forwardKey("ENTER");
      }
      // BACKSPACE → DEL
      if (letter === "BACKSPACE") {
        this.forwardKey("DEL");
      }
    },
  },
  mounted() {
    this.physkey();
  },
};
</script>

<template>
  <div class="keysState">
    <KeyboardKey
      v-for="(key, index) in keysState"
      :key="index"
      :keyData="key"
      @key-pressed="forwardKey"
    />
  </div>
</template>
