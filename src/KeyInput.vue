<!-- <script>
export default {};
</script> -->

<template>
  <div class="keysState">
    <KeyboardKey
      v-for="(key, index) in keysState"
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
    keysState: Array
  },
  methods: {
    forwardKey(keyValue) {
      this.$emit("KeyValue", keyValue);
    }
  }
};
</script>

