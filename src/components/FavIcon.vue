<template>
  <div>
    <button @click="loading">修改图标</button>
  </div>
</template>

<script setup>
import useFavicon from '../utils/favicon';
let { favicon } = useFavicon()
function loading () {
  favicon.value = '../assets/avatar.png'
  console.log(favicon);
}
</script>

<style lang="scss" scoped>
</style>