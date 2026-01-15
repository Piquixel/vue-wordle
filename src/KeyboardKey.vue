<!-- <script>
export default {};
</script> -->

<template>
  <button
    class="key"
    :class="keyData.status"
    @click="handleClick"
  >
    {{ keyData.key }}
  </button>
</template>

<script>
export default {
  props: {
    keyData: Object
  },
  methods: {
    handleClick() {
      this.$emit("key-pressed", this.keyData.key);
    }
  }
};
</script>

