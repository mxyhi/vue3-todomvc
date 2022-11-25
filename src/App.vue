<script setup lang="ts">
import Todo from '@/components/todo/index.vue';
import { customRef, reactive, ref, watch } from 'vue';

const size = ref(36);
const options = reactive({ size: `${size.value}px` });
watch(size, (newVal, oldVal) => {
  if (newVal !== oldVal) {
    options.size = `${newVal}px`;
  }
});
</script>

<template>
  <div>
    视图大小设置(max:200px,min:8px)
    <div>
      <input type="range" v-model="size" min="8" max="200" step="1" /><span>{{
        size
      }}</span
      >px
    </div>
  </div>
  <main class="todo-box">
    <Todo :options="options" />
  </main>
</template>

<style lang="scss" scoped>
@import './components/todo/index.scss';

div {
  input[type='range'] {
    // width: calc($size * 6);
    width: 600px;
  }
  .todo-box {
    display: flex;
    justify-content: center;
    margin-top: 8vh;
  }
}
</style>
