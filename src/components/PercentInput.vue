<template>
  <label>
    <input
      type="number"
      :min="min"
      :max="max"
      :value="currentValue"
      class="custom-input"
      @change="$emit('percentChanged', $event.target.value)"
    />
    % {{ label }}</label
  >
</template>

<script>
export default {
  props: {
    label: {
      type: String,
      default: 'Enter a percentage'
    },
    min: {
      type: Number,
      default: 0
    },
    max: {
      type: Number,
      default: 100
    },
    currentValue: {
      type: Number,
      default: 0
    }
  }
}
</script>
<style scoped>
label {
  display: flex;
  align-items: center;
}

input {
  width: 50px;
  margin-right: 8px;
  display: block;
}
</style>
