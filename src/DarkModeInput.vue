<script>
export default {
  props: {
    darkMode: {
      type: Boolean,
      required: true,
    },
  },
  methods: {
    toggleDark(e) {
      this.$emit("toggleDark", {
        x: e.clientX,
        y: e.clientY,
      });
    },
  },
};
</script>

<template>
  <button class="dark-toggle" @keydown.enter.prevent @click="toggleDark">
    <img v-if="darkMode" class="scheme-icon" src="/src/darkMode.svg" alt="color scheme icon" />
    <img v-else class="scheme-icon" src="/src/lightMode.svg" alt="color scheme icon" />
  </button>
</template>
