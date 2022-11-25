<template>
  <div ref="s">
    <input
      type="checkbox"
      class="select"
      v-model="select"
      @click="changeSelect"
    />
    <input
      type="text"
      v-model.trim="todo"
      class="inp"
      placeholder="请输入待办事项."
      @keyup.enter="addItem"
    />
    <button @click="addItem">+</button>
  </div>
</template>

<script setup lang="ts">
import type { Todo } from '../index.vue';
import { inject, ref, type Ref } from 'vue';

const props = defineProps<{
  changeSelectAll: (done: boolean) => void;
}>();

const todo = ref('');
const addTodo = inject<(todo: Todo) => false | number>('addTodo')!;
const select = inject<Ref<boolean>>('selectAll')!;

const changeSelect = () => props.changeSelectAll(!select.value);

const addItem = () => {
  if (todo.value.length !== 0) {
    if (!addTodo(todo.value)) {
      alert('待办事项已存在');
      return;
    }
    todo.value = '';
  }
};
</script>

<style lang="scss" scoped>
@import '../index.scss';
div {
  display: flex;
  align-items: center;
  .select {
    margin-right: 0;
    width: $size;
    height: $size;
    outline: none;
  }
  .inp {
    outline: none;
    height: $size;
    width: calc($size * 10);
    box-sizing: border-box;
    font-size: calc($size / 2);
  }
  button {
    width: $size;
    height: $size;
    font-size: calc($size / 1.2);
  }
}
</style>
