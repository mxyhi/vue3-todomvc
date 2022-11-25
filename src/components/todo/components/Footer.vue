<template>
  <main>
    <div>
      <span>{{ outstanding }}</span
      >项未完成
    </div>
    <div @click="handleActive($event.target as HTMLDivElement)">
      <div :class="{ active: selected === Selected.All }">
        {{ Selected.All }}
      </div>
      <div :class="{ active: selected === Selected.Active }">
        {{ Selected.Active }}
      </div>
      <div :class="{ active: selected === Selected.Completed }">
        {{ Selected.Completed }}
      </div>
    </div>
    <div>
      <span v-show="todoList.length - outstanding" @click="deleteCompleted"
        >删除已完成</span
      >
    </div>
  </main>
</template>

<script setup lang="ts">
import { computed, inject, ref } from 'vue';
import type { TodoListOptions } from '../index.vue';

enum Selected {
  All = '所有',
  Active = '未完成',
  Completed = '已完成',
}

const todoList = inject<TodoListOptions[]>('getTodoList')!;
const changeActive = inject<(active: number) => void>('changeActive');
const deleteTodo = inject<(index: number) => boolean>('deleteTodo')!;

const selected = ref(Selected.All);

const outstanding = computed(() =>
  todoList.reduce((pre, current) => {
    current.done || pre++;
    return pre;
  }, 0)
);

const handleActive = (el: HTMLDivElement) => {
  switch (el.textContent?.trim()) {
    case Selected.All:
      selected.value = Selected.All;
      changeActive?.(0);
      break;
    case Selected.Active:
      selected.value = Selected.Active;
      changeActive?.(1);
      break;
    case Selected.Completed:
      selected.value = Selected.Completed;
      changeActive?.(2);
      break;
  }
};

const deleteCompleted = () => {
  const indexArr = todoList.map((todo, index) => {
    if (todo.done) {
      return index;
    }
    return null;
  });
  indexArr.forEach(i => {
    if (i !== null) {
      todoList.splice(
        todoList.findIndex(item => item.done),
        1
      );
    }
  });
};
</script>

<style lang="scss" scoped>
@import '../index.scss';
main {
  display: flex;
  justify-content: space-between;
  font-size: calc($size / 2.5);
  height: $size;
  align-items: center;
  color: #777;
  div {
    &:nth-child(1) {
      span {
        font-size: calc($size / 2.5);
        margin: 0 calc($size / 5) 0 calc($size / 2.8);
      }
    }
    &:nth-child(2) {
      display: flex;
      font-size: calc($size / 2.5);
      justify-content: space-between;
      div {
        cursor: pointer;
        margin: 0 calc($size / 12);
        padding: calc($size / 12) calc($size / 6);
        border-radius: calc($size / 8);
        box-sizing: border-box;
        text-decoration: none;
        font-size: calc($size / 2.5);
      }
    }
    &:nth-child(3) {
      padding-right: calc($size / 8);
      text-decoration: underline;
      cursor: pointer;
      width: calc($size * 2);
      font-size: calc($size / 2.5);
    }
  }
}

.active {
  border: 1px solid #f39c12;
}
</style>
