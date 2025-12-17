<script>
import WordLine from "./WordLine.vue";
export default {
  components: { WordLine },
  props: {
    attempts: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {};
  },
};
</script>

<template>
  <table border>
    <tbody>
      <WordLine v-for="word in attempts" :key="word" :input="attempts[word - 1]"></WordLine>
    </tbody>
  </table>
</template>
