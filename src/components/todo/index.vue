<template>
  <main>
    <Header :changeSelectAll="changeSelectAll" />
    <List />
    <Footer />
  </main>
</template>

<script setup lang="ts">
export type Todo = string;
export interface TodoListOptions {
  id: string;
  todo: Todo;
  done: boolean;
}

import Header from './components/Header.vue';
import List from './components/List.vue';
import Footer from './components/Footer.vue';
import { provide, reactive, ref, watch, watchEffect } from 'vue';

const props = defineProps<{
  /**
   * @description 配置
   */
  options?: {
    /**
     * @Default `40px`
     * @description size
     */
    size?: string;
  };
}>();

watchEffect(() => {
  document
    .getElementsByTagName('body')[0]
    .style.setProperty('--size', props.options?.size ?? '40px');
  console.log(props.options?.size);
});

const getList = (): TodoListOptions[] =>
  JSON.parse(localStorage.getItem('todoList') || '[]');
const setList = (todoList: TodoListOptions[]) => {
  localStorage.setItem('todoList', JSON.stringify(todoList));
};

const todoList = reactive<TodoListOptions[]>(getList());
const selectAll = ref(false);

watch(todoList, () => {
  selectAll.value = todoList.length ? todoList.every(todo => todo.done) : false;
  setList(todoList);
  console.table(todoList);
});
const changeSelectAll = (done: boolean) =>
  done
    ? todoList.forEach(todo => (todo.done = true))
    : todoList.forEach(todo => (todo.done = false));
const addTodo = (todo: Todo): false | number => {
  if (todoList.find(item => item.todo === todo)) {
    return false;
  }
  return todoList.push({
    id: Date.now().toString(),
    todo,
    done: false,
  });
};

const deleteTodo = (index: number): boolean => {
  const res = todoList.splice(index, 1);
  return res.length ? true : false;
};
const changeList = (callback: () => void): void => {
  watchEffect(() => {
    console.log(`change ${todoList?.[0]}`);
    callback();
  });
};
const activeView = ref(0);
const changeActive = (active: number) => {
  activeView.value !== active && (activeView.value = active);
};
provide('getTodoList', todoList);
provide('deleteTodo', deleteTodo);
provide('addTodo', addTodo);
provide('selectAll', selectAll);
provide('changeList', changeList);
provide('active', activeView);
provide('changeActive', changeActive);
</script>

<style lang="scss" scoped></style>
