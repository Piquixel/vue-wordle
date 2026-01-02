<script>
import WordCell from "./WordCell.vue";
export default {
  components: { WordCell },
  props: {
    wordToGuess: {
      type: String,
      required: true,
    },
  },
  data() {
    return {};
  },
  methods: {},
};
</script>

<template>
  <tr>
    <WordCell v-for="n in wordToGuess" :key="n"></WordCell>
  </tr>
</template>
