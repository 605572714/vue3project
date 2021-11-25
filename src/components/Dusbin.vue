<template>
  <span class="dustbin">🗑</span>
  <div class="animation-wrap">
    <transition @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
      <div class="animate" v-show="animate.show">📋</div>
    </transition>
  </div>
</template>

<script setup>
import { reactive } from "vue";

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
  todos.value.slice(i, 1)
}
</script>

<style lang="scss" scoped>
.animate-wrap .animate {
  position: fixed;
  right: 10px;
  top: 10px;
  z-index: 100;
  transition: all 0.5s linear;
}
</style>