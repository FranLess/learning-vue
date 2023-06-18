<script setup>
import { uid } from "uid";
import { ref, watch, computed } from "vue";
import TodoCreator from "../components/TodoCreator.vue";
import TodoItem from "../components/TodoItem.vue";
import { Icon } from "@iconify/vue";

const TODO_LIST_KEY = "todoList";

const todoList = ref([]);

watch(
  todoList,
  () => {
    setTodoListLocalStorage();
  },
  {
    deep: true,
  }
);

// Computed function will be executed every time todoList changes,
// it would no happend if we make a ref()
const todoCompleted = computed(() => {
  return todoList.value.every((item) => item.isCompleted);
});

const fetchTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem(TODO_LIST_KEY));
  if (savedTodoList) todoList.value = savedTodoList;
};

fetchTodoList();

const setTodoListLocalStorage = () => {
  localStorage.setItem(TODO_LIST_KEY, JSON.stringify(todoList.value));
};

const createTodo = (todo) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: false,
    isEditing: false,
  });
};

const updateTodo = (value, position) => {
  todoList.value[position].todo = value;
};

const deleteTodo = (id) => {
  todoList.value = todoList.value.filter((item) => item.id !== id);
};

const toggleTodoComplete = (position) => {
  todoList.value[position].isCompleted = !todoList.value[position].isCompleted;
};

const toggleEditTodo = (position) => {
  todoList.value[position].isEditing = !todoList.value[position].isEditing;
};
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul v-if="todoList.length > 0" class="todo-list">
      <TodoItem
        v-for="(todo, index) in todoList"
        :todo="todo"
        :index="index"
        @toggle-complete="toggleTodoComplete"
        @edit-todo="toggleEditTodo"
        @update-todo="updateTodo"
        @delete-todo="deleteTodo" />
    </ul>
    <p v-else="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:sad-but-relieved-face" width="22" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:sad-but-relieved-face" width="22" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
  </main>
</template>

<style scoped lang="scss">
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>
