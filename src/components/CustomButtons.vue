<template>
  <button class="btn" :class="customClass">
    <slot></slot>
  </button>
</template>

<script>
export default {
  props: {
    btnType: {
      type: String,
      default: ''
    }
  },
  computed: {
    customClass() {
      if (this.btnType) {
        return `btn-${this.btnType}`
      } else {
        return ''
      }
    }
  }
}
</script>

<style scoped>
.btn {
  border-radius: 4px;
  padding: 8px 10px;
  border: none;
  font-size: 16px;
  display: block;
  width: 100%;
  background-color: #82c0cc;
}

.btn:hover {
  background-color: #73aab5;
  cursor: pointer;
}

.btn-primary {
  background-color: var(--color-black);
  color: white;
}

.btn-primary:hover {
  background-color: var(--color-black);
}

.btn-primary-right {
  background-color: var(--color-black);
  color: white;
  width: auto;
  margin-left: auto;
  margin-bottom: 20px;
}

.btn-primary-right:hover {
  background-color: var(--color-black);
}

.btn-secondary {
  background-color: #3943b7;
  color: white;
}

.btn-secondary:hover {
  background-color: #30399e;
}
</style>
