<template>
  <div class="sidebar">
    <slot></slot>
  </div>
</template>

<style>
.sidebar {
  width: 300px;
  padding: 20px 20px 20px 30px;
  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  background-color: rgb(230, 230, 230);
}
.sidebar-section {
  background-color: #fff;
  padding: 15px 10px 5px 10px;
  border-radius: 5px;
  margin-top: 20px;
}
.sidebar-title {
  font-weight: 600;
  margin-bottom: 15px;
}
.sidebar label {
  margin-bottom: 10px;
}
</style>
