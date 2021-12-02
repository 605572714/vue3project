<template>
  <div>
    <span class="dustbin">🗑</span>
    <input type="text" v-model="title" @keydown.enter="addTodo" />
    <ul v-if="todos.length">
      <transition-group name="flip-list" tag="ul">
        <li v-for="(todo,i) in todos" :key="todo">
          <input type="checkbox" v-model="todo.done" />
          <span :class="{ done: todo.done }">{{ todo.title }}</span>
          <span class="remove-btn" @click="removeTodo($event, i)">❌</span>
        </li>
        <div class="animate-wrap">
          <transition @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
            <div class="animate" v-show="animate.show">📋</div>
          </transition>
        </div>
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
import { ref, computed, watchEffect, reactive } from 'vue'

let { title, showModal, todos, addTodo, clear, active, all, allDone } = useTodos()
function useTodos () {
  let title = ref("")
  let showModal = ref(false)
  let todos = useStorage('todos', [{ title: '学习vue', done: false }])
  function addTodo () {
    // debugger
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

let animate = reactive({
  show: false,
  el: null
})
function beforeEnter (el) {
  let dom = animate.el;
  let rect = dom.getBoundingClientRect();
  let x = window.innerWidth - rect.left - 60;
  let y = rect.top - 10;
  el.style.transform = `translate(-${x}px,${y}px)`
}

function enter (el, done) {
  document.body.offsetHeight;
  el.style.transform = `translate(0,0)`
  el.addEventListener(`transitioned`, done)
}
function afterEnter (el) {
  animate.show = false;
  el.style.display = "none"
}
function removeTodo (e, i) {
  animate.el = e.target;
  animate.show = true;
  todos.value.splice(i, 1)
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
.remove-btn {
  margin-left: 10px;
}
.dustbin {
  position: fixed;
  right: 10px;
  top: 10px;
  z-index: 100;
}
.animate-wrap .animate {
  position: fixed;
  right: 10px;
  top: 10px;
  z-index: 100;
  transition: all 0.5s linear;
}
</style>