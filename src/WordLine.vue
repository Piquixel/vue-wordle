<script>
import WordCell from "./WordCell.vue";
export default {
  components: { WordCell },
  props: {
    input: {
      type: String,
      default: "     ",
    },
  },
  data() {
    return {
      wordLength: 5,
    };
  },
};
</script>

<template>
  <tr>
    <WordCell v-for="n in wordLength" :key="n">{{ input[n] }}</WordCell>
  </tr>
</template>
