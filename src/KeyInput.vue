<!-- <script>
export default {};
</script> -->

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

<script>
  import KeyboardKey from "./KeyboardKey.vue";

  export default {
    components: { KeyboardKey },
    props: {
      keysState: Array
    },
    methods: {
      //remonter la valeur de la touche appuyée au composant parent GameBoard
      forwardKey(keyValue) {
        this.$emit("KeyValue", keyValue);
      },
      //gestion du clavier physique
      physkey(){
        window.addEventListener('keyup', this.handlePhysicalKeyboard);
      },
      handlePhysicalKeyboard(event) {
        let letter = event.key.toUpperCase();
        // Lettres A-Z
        if (/^[A-Z]$/.test(letter)) {
          this.forwardKey(letter);
        }
        // Lettres accentuées
        if (letter === "É" || letter === "È" || letter === "Ê" || letter === "Ë") {
          this.forwardKey("E");
        }
        if (letter === "À" || letter === "Â") {
          this.forwardKey("A");
        }
        if (letter === "Ç") {
          this.forwardKey("C");
        }
        if (letter === "Ù" || letter === "Û") {
          this.forwardKey("U");
        }
        if (letter === "Ô") {
          this.forwardKey("O");
        }
        if (letter === "Î" || letter === "Ï") {
          this.forwardKey("I");
        }
        // ENTER
        if (letter === "ENTER") {
          this.forwardKey("ENTER");
        }

        // BACKSPACE → DEL
        if (letter === "BACKSPACE") {
          this.forwardKey("DEL");
        }
      }
    },
    mounted() {
      this.physkey();
    }
  };

</script>

