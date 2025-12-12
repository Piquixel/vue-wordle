<script>
export default {
  props: {
    char: {
      type: String,
      validator: (value) => value.length < 2,
      default: "",
    },
  },
};
</script>

<template>
  <td>
    <slot></slot>
  </td>
</template>
