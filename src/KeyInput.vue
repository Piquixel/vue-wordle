<!-- <script>
export default {};
</script> -->

<template>
  <div class="keyboard">
    <KeyboardKey
      v-for="(key, index) in keyboard"
      :key="index"
      :keyData="key"
      @key-pressed="forwardKey"
    />
  </div>
</template>

<script>
import KeyboardKey from "./KeyboardKey.vue";

export default {
  components: { KeyboardKey },
  props: {
    keyboard: Array
  },
  methods: {
    forwardKey(keyValue) {
      this.$emit("KeyInput", keyValue);
    }
  }
};
</script>

