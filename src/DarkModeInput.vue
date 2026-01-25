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
  <button class="material-symbols-rounded dark-toggle" @keydown.enter.prevent @click="toggleDark">
    <slot></slot>
  </button>
</template>
