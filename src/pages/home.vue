<template>
  <div>这是首页</div>
  <TodoList></TodoList>
  <FavIcon></FavIcon>
  <h1 @click="toggle">click</h1>
</template>

<script setup>
import TodoList from '../components/TodoList.vue';
import FavIcon from '../components/FavIcon.vue';
import { useFullscreen } from "@vueuse/core"
const { isFullscreen, enter, exit, toggle } = useFullscreen()
</script>

<style lang="scss" scoped>
</style>