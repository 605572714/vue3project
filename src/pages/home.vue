<template>
  <div>这是首页</div>
  <TodoList></TodoList>
  <FavIcon></FavIcon>
  <Rate v-model="score" theme="yellow">
    <img src="../assets/avatar.png" width="16" alt />
  </Rate>
  <!-- <h1 @click="toggle">click</h1> -->
</template>

<script setup>
import TodoList from '../components/TodoList.vue';
import FavIcon from '../components/FavIcon.vue';
import Rate from '../components/Rate.vue';
import { useFullscreen } from "@vueuse/core";
import { ref } from 'vue';
const { isFullscreen, enter, exit, toggle } = useFullscreen()
let score = ref(2)
// function update (num) {
//   score.value = num
// }
</script>

<style lang="scss" scoped>
</style>