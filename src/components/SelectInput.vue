<template>
  <label>
    <select class="custom-input" @change="$emit('upgradeSelection', $event.target.value)">
      <option
        v-for="option in options"
        :key="option.value"
        :value="option.value"
        :selected="defaultValue === option.value"
      >
        {{ option.name }}
      </option>
    </select>
    {{ label }}
  </label>
</template>

<script>
export default {
  props: {
    label: {
      type: String,
      default: 'Choose a font weight'
    },
    options: {
      type: Array
    },
    defaultValue: {
      type: String
    }
  }
}
</script>

<style scoped>
label {
  display: flex;
  align-items: center;
}

select {
  display: inline-block;
  min-width: 80px;
  margin-right: 8px;
}
</style>
