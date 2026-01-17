<template>
  <button
    class="key"
    :class="[
      { ['char--wrong']: keyData.status == 'wrong' },
      { ['char--misplaced']: keyData.status == 'misplaced' },
      { ['char--correct']: keyData.status == 'correct' },
      { ['keyWide']: keyData.status == 'wide' },
    ]"
    @click="handleClick"
  >
    {{ keyData.key }}
  </button>
</template>

<script>
export default {
  props: {
    keyData: Object,
  },
  methods: {
    handleClick() {
      this.$emit("key-pressed", this.keyData.key);
    },
  },
};
</script>
