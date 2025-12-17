<script>
import WordCell from "./WordCell.vue";
export default {
  components: { WordCell },
  props: {},
  data() {
    return {};
  },
  methods: {},
};
</script>

<template>
  <tr>
    <WordCell v-for="n in wordLength" :key="n"></WordCell>
  </tr>
</template>
