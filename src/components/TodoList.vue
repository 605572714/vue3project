<template>
  <div>
    <input type="text" v-model="title" @keydown.enter="addTodo" />
    <ul v-if="todos.length">
      <li v-for="todo in todos" :key="todo">
        <input type="checkbox" v-model="todo.done" />
        <span :class="{ done: todo.done }">{{ todo.title }}</span>
      </li>
    </ul>
    <div v-else>暂无数据</div>
    <div>
      全选
      <input type="checkbox" v-model="allDone" />
      <span>{{ active }}/{{ all }}</span>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watchEffect } from 'vue'

let { title, todos, addTodo, clear, active, all, allDone } = useTodos()

function useTodos () {
  let title = ref("")
  let todos = useStorage('todos', [{ title: '学习vue', done: false }])
  function addTodo () {
    todos.value.push({
      title: title.value,
      done: false
    })
    title.value = ""
  }
  function clear () {
    todos.value = todos.value.filter((v) => !v.done)
  }
  let active = computed(() => {
    return todos.value.filter((v) => !v.done).length
  })
  let all = computed(() => todos.value.length)
  let allDone = computed({
    get: function () {
      return active.value === 0
    },
    set: function (value) {
      todos.value.forEach((todo) => {
        todo.done = value
      })
    }
  })
  return { title, todos, addTodo, clear, active, all, allDone }
}

function useStorage (name, value = []) {
  let data = ref(JSON.parse(localStorage.getItem(name) || JSON.stringify(value)));
  watchEffect(() => {
    localStorage.setItem(name, JSON.stringify(data.value))
  })
  return data
}

</script>

<style scoped>
h1 {
  color: red;
}
</style>