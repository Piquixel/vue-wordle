<script>
import WordLine from "./WordLine.vue";
export default {
  components: { WordLine },
  props: {},
  data() {
    return {};
  },
  methods: {},
};
</script>

<template>
  <table border>
    <tbody>
      <WordLine v-for="word in attempts" :key="word"></WordLine>
    </tbody>
  </table>
</template>
