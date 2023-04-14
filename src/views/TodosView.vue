<template>
  <main>
    <h1>Create Todo</h1>
    <CreateTodo @create-todo="createTodo"/>
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem
          v-for="(todo, index) in todoList"
          :key="todo.id" :todo="todo"
          :index="index"
          @toggle-complete="toggleTodoCompete"
          @edit-todo="toggleEditTodo"
          @update-todo="updateTodo"
          @delete-todo="deleteTodo"
      />
    </ul>
    <p class="todos-msg" v-else>
      <Icon icon="noto-v1:sad-but-relieved-face" color="#41b080" width="22" />
      <span>You have no todo`s to complete! Add one!</span>
    </p>
  </main>
</template>

<script setup lang="ts">
import {ref} from "vue";
import {uid} from "uid";
import CreateTodo from "@/components/CreateTodo.vue";
import TodoItem from "@/components/TodoItem.vue";
import {Icon} from "@iconify/vue";

interface ITodoList {
  id: Number | String,
  todo: String,
  isCompleted: Boolean,
  isEditing: Boolean
}

const todoList = ref<ITodoList[]>([])

const fetchTodoList = () => {
  const savedTodoList = localStorage.getItem('todoList')
  if ( savedTodoList !== null) {
    todoList.value = JSON.parse(savedTodoList)
  }
}
fetchTodoList()

const setTodoListLocalStorage = () => {
  localStorage.setItem('todoList', JSON.stringify((todoList.value)))
}

const createTodo = (todo: String) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: false,
    isEditing: false,
  })
  setTodoListLocalStorage()
}
const toggleTodoCompete = (todoPos: number) => {
  todoList.value[todoPos].isCompleted = !todoList.value[todoPos].isCompleted
  setTodoListLocalStorage()
}
const toggleEditTodo = (todoPos: number) => {
  todoList.value[todoPos].isEditing = !todoList.value[todoPos].isEditing
  setTodoListLocalStorage()
}
const updateTodo = (todoVal: String, todoPos: number) => {
  todoList.value[todoPos].todo = todoVal
}
const deleteTodo = (todoId: String) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoId)
  setTodoListLocalStorage()
}
</script>


<style lang="scss" scoped>
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
