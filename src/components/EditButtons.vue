<template>
  <button class="edit-btn edit-btn-add" @click="handleAddClick" v-if="showAddBtn">
    {{ textAdd }}
  </button>
  <button class="edit-btn edit-btn-remove" @click="handleRemoveClick" v-if="showRemoveBtn">
    {{ textRemove }}
  </button>
</template>

<script>
export default {
  name: 'EditButtons',
  emits: ['add-click', 'remove-click'],
  props: {
    showRemoveBtn: {
      type: Boolean,
      default: true
    },
    showAddBtn: {
      type: Boolean,
      default: true
    },
    textAdd: {
      type: String,
      default: '+'
    },
    textRemove: {
      type: String,
      default: '-'
    }
  },
  methods: {
    handleAddClick() {
      this.$emit('add-click')
    },
    handleRemoveClick() {
      this.$emit('remove-click')
    }
  }
}
</script>

<style scoped>
.edit-off .edit-btn {
  display: none;
}
.edit-off .edit-btn {
  display: none;
}

.edit-btn {
  min-width: 20px;
  height: 20px;
  border-radius: 4px;
  border: none;
  background-color: transparent;
  margin-left: 8px;
}

.edit-btn-add {
  background-color: #3feee6;
  color: #000;
}

.edit-btn-add:hover {
  background-color: rgb(68, 219, 212);
}

.edit-btn-remove {
  background-color: #f97068;
  color: #fff;
}

.edit-btn-remove:hover {
  background-color: #e0635c;
}
</style>
