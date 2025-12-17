<script>
import WordCell from "./WordCell.vue";
export default {
  components: { WordCell },
  props: {
    input: {
      type: String,
      default: "",
      required: true,
    },
  },
  data() {
    return {
      wordLength: 5,
      guess: this.input.padEnd(this.wordLength),
    };
  },
};
</script>

<template>
  <tr>
    <WordCell v-for="n in wordLength" :key="n">{{ guess[n - 1] }}</WordCell>
  </tr>
</template>
