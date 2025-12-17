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
    return {
      // attemptsLength: this.attempts.length,
    };
  },
};
</script>

<template>
  <table border>
    <tbody>
      <WordLine v-for="word in attempts" :key="word"></WordLine>
    </tbody>
  </table>
</template>
