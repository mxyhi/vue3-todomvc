<template>
  <ul>
    <li v-for="(todo, index) in activeTodoList" :key="todo.id">
      <input type="checkbox" class="select" v-model="todo.done" /><span
        :contenteditable="writable"
        @dblclick="changeWritable"
        @blur="changeTodoItem($event.target as HTMLSpanElement, index)"
        @keyup.enter="changeTodoItem($event.target as HTMLSpanElement, index)"
        >{{ todo.todo }}</span
      >
      <button @click="deleteTodo(index)">删除</button>
    </li>
  </ul>
</template>

<script setup lang="ts">
import { computed, inject, ref, type Ref } from 'vue';
import type { TodoListOptions } from '../index.vue';

const todoList = inject<TodoListOptions[]>('getTodoList')!;
const deleteTodo = inject<(id: number) => boolean>('deleteTodo')!;
const activeView = inject<Ref<number>>('active')!;

const activeTodoList = computed(() => {
  switch (activeView.value) {
    case 0:
      return todoList;
    case 1:
      return todoList.filter(item => !item.done);
    case 2:
      return todoList.filter(item => item.done);
  }
});

const writable = ref(false);
const changeWritable = () => {
  writable.value = true;
};
const changeTodoItem = (e: HTMLSpanElement, index: number) => {
  const todo = e.textContent?.trim()!;
  if (todo !== todoList[index].todo) {
    if (!todoList.find(item => item.todo === todo)) {
      todoList[index].todo = todo;
    } else {
      e.textContent = todoList[index].todo;
    }
  }
  writable.value = false;
};
</script>

<style lang="scss" scoped>
@import '../index.scss';
ul {
  width: calc($size * 12);
  li {
    border: 1px solid #ccc;
    display: flex;
    align-items: center;
    .select {
      width: $size;
      height: $size;
      outline: none;
    }
    span {
      margin: 0 calc($size / 5);
      font-size: calc($size / 1.8);
      width: calc($size * 9.6);
      height: $size;
      line-height: $size;
      box-sizing: border-box;
      &:focus {
        border: 1px solid #ccc;
        box-shadow: 0px 0px calc($size / 8) 0px #ccc inset;
      }
    }
    button {
      width: $size;
      height: $size;
      font-size: calc($size / 3);
    }
  }
}
</style>
