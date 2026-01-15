<!-- <script>
export default {};
</script> -->

<template>
  <button
    class="key"
    :class="[{['keyWrong']: keyData.status == 'wrong'}, {['keyMisplaced']: keyData.status == 'misplaced'}, {['keyCorrect']:keyData.status == 'correct'}]"
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

