<script>
import WordLine from "./WordLine.vue";
export default {
  components: { WordLine },
  data() {
    return {
      attempts: 6,
    };
  },
};
</script>

<template>
  <table border>
    <tbody>
      <WordLine v-for="attempt in attempts" :key="attempt"></WordLine>
    </tbody>
  </table>
</template>
