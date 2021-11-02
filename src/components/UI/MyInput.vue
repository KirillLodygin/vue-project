<template>
  <input :value='modelValue' @input='updateInput' class='input' :type={type}>
</template>

<script>
export default {
  name: 'my-input',
  props: {
    type: {
      type: String,
      required: true,
    },
    modelValue: [String, Number]
  },
  methods: {
    updateInput(event) {
      this.$emit('update:modelValue', event.target.value)
    }
  }
};
</script>

<style scoped>
  .input {
    padding: 10px;
    border: 1px solid teal;
    width: 100%;
  }
</style>
