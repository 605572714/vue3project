<template>
  <div>
    <input type="text" v-model="title" @keydown.enter="addTodo" />
    <ul v-if="todos.length">
      <transition-group name="flip-list" tag="ul">
        <li v-for="todo in todos" :key="todo">
          <input type="checkbox" v-model="todo.done" />
          <span :class="{ done: todo.done }">{{ todo.title }}</span>
        </li>
      </transition-group>
    </ul>
    <div v-else>暂无数据</div>
    <div>
      全选
      <input type="checkbox" v-model="allDone" />
      <span>{{ active }}/{{ all }}</span>
    </div>
    <transition name="modal">
      <div class="info-wrapper" v-if="showModal">
        <div class="info">未输入任何消息</div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, computed, watchEffect } from 'vue'

let { title, showModal, todos, addTodo, clear, active, all, allDone } = useTodos()
function useTodos () {
  let title = ref("")
  let showModal = ref(false)
  let todos = useStorage('todos', [{ title: '学习vue', done: false }])
  function addTodo () {
    if (!title.value) {
      showModal.value = true;
      setTimeout(() => {
        showModal.value = false
      }, 1500);
      return;
    }
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
  return { title, showModal, todos, addTodo, clear, active, all, allDone }
}

function useStorage (name, value = []) {
  let data = ref(JSON.parse(localStorage.getItem(name) || JSON.stringify(value)));
  watchEffect(() => {
    localStorage.setItem(name, JSON.stringify(data.value))
  })
  return data
}

</script>
<style>
h1 {
  color: red;
}
.info-wrapper {
  position: fixed;
  top: 20px;
  width: 200px;
}
.info {
  padding: 20px;
  color: white;
  background: #d88986;
}
.modal-enter-from,
.modal-leave-to {
  opacity: 0;
  transform: translateY(-60px);
}
.modal-enter-active,
.modal-leave-active {
  transition: all 0.3s ease;
}
.flip-list-move {
  transition: transform 0.8s ease;
}
.flip-list-enter-active,
.flip-list-leave-active {
  transition: all 1s ease;
}
.flip-list-enter-from,
.flip-list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>