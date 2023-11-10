<script setup>
import { computed, onMounted, ref } from 'vue';

const STORAGE_KEY = 'todoapp-vue3'

const newTodo = ref("");
const todoList = ref(JSON.parse(localStorage.getItem(STORAGE_KEY)) || defaultData);

const selectedFilter = ref('all');
const filteredList = computed(() => filters[selectedFilter.value](todoList.value))

const defaultData = [{
  done: false,
  content: 'Write a blog post'
}];

const filters = {
  all: (todoList) => todoList,
  active: (todoList) => todoList.filter((todo) => !todo.done),
  completed: (todoList) => todoList.filter((todo) => todo.done),
}

function changeFilter(filter) {
  selectedFilter.value = filter;
}

function createTodo() {
  if (newTodo.value) {
    todoList.value.push({
      done: false,
      content: newTodo.value,
    }),
      newTodo.value = '';
  }
  saveData();
}

function removeTodo(index) {
  todoList.value.splice(index, 1);
  saveData();
}

function doneTodo(todo) {
  todo.done = !todo.done;
  saveData();
}

function saveData() {
  let data = JSON.stringify(todoList.value);
  localStorage.setItem(STORAGE_KEY, data);
}
</script>

<template>
  <h1>ToDo App with Vue3</h1>
  <form @submit.prevent="createTodo()">
    <label>Create a new Todo</label>
    <input v-model="newTodo" autocomplete="false" autofocus>
    <button>Create Todo</button>
  </form>
  <h2>Todo List:</h2>
  <div class="filters">
    <a @click="changeFilter('all')" :class="{ selectedFilter: selectedFilter === 'all' }">All</a>
    <a @click="changeFilter('active')" :class="{ selectedFilter: selectedFilter === 'active' }">Active</a>
    <a @click="changeFilter('completed')" :class="{ selectedFilter: selectedFilter === 'completed' }">Completed</a>
  </div>
  <TransitionGroup tag="ul" name="fade">
    <li v-for="(todo, index) in filteredList" :key="index">
      <span @click="doneTodo(todo)" :class="{ done: todo.done }">{{ todo.content }}</span>
      <button @click="removeTodo(index)">X</button>
    </li>
  </TransitionGroup>
  <h4 v-if="todoList.length < 1">Empty list.</h4>
</template>

<style lang="scss">
$border: 2px solid rgba($color: white, $alpha: 0.35);
$size1: 6px;
$size2: 12px;
$size3: 18px;
$size4: 24px;
$size5: 48px;
$footer-height: 60px;
$backgroundColor: #3854A6;
$textColor: white;
$inverseTextColor: black;
$primaryColor: #8552F2;
$secondTextColor: #F2E30F;

body {
  margin: 0;
  padding: 0;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: $backgroundColor;
  color: $textColor;

  #app {
    max-width: 600px;
    margin-left: auto;
    margin-right: auto;
    padding: 20px;

    h1 {
      font-weight: bold;
      font-size: 28px;
      text-align: center;
      text-shadow: -1px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
    }

    form {
      display: flex;
      flex-direction: column;
      width: 100%;

      label {
        font-size: 14px;
        font-weight: bold;
      }

      input,
      button {
        height: $size5;
        box-shadow: none;
        outline: none;
        padding-left: $size2;
        padding-right: $size2;
        border-radius: $size1;
        font-size: 18px;
        margin-top: $size1;
        margin-bottom: $size2;
      }

      input {
        background-color: transparent;
        border: $border;
        color: inherit;
      }
    }

    button {
      cursor: pointer;
      background-color: $primaryColor;
      border: 1px solid $primaryColor;
      color: $secondTextColor;
      font-weight: bold;
      outline: none;
      border-radius: $size1;
    }

    h2 {
      font-size: 22px;
      border-bottom: $border;
      padding-bottom: $size1;
    }

    ul {
      padding: 10px;

      li {
        display: flex;
        justify-content: space-between;
        align-items: center;
        border: $border;
        padding: $size2 $size4;
        border-radius: $size1;
        margin-bottom: $size2;
        box-sizing: border-box;

        span {
          cursor: pointer;
        }

        .done {
          text-decoration: line-through;
        }

        button {
          font-size: $size2;
          padding: $size1;
        }
      }
    }

    h4 {
      text-align: center;
      opacity: 0.5;
      margin: 0;
    }

    .filters {
      text-align: center;
      display: flex;
      justify-content: space-between;
    }

    a {
      flex: 1;
      margin: 0 $size1;
      border: 1px solid $secondTextColor;
      color: $secondTextColor;
      padding: $size1;
    }

    .selectedFilter {
      background-color: $secondTextColor;
      color: $inverseTextColor;
    }

    .fade-enter-active,
    .fade-leave-active {
      transition: all 0.5s ease;
    }

    .fade-enter-from,
    .fade-leave-to {
      opacity: 0;
      transform: translateX(30px);
    }
  }
}
</style>