<template>
  <div class="container">
    <h1 class="title">ToDo List</h1>
    <div style="display: flex">
      <input type="text" placeholder="Enter Your Data" v-model="newTodo" />
      <button @click="addTodo">Add Todo</button>
    </div>
    <div>
      <div class="todo-box" v-for="(todo, index) in todos" :key="index">
        <h3 :class="{ completed: todo.completed }">{{ todo.title }}</h3>
        <div>
          <button @click="removeTodo(index)">Delete</button>
          <button @click="completeTodo(index)">
            {{ todo.completed ? "Undo" : "Complete" }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive, toRefs } from "vue";
export default {
  name: "App",
  setup() {
    const state = reactive({
      newTodo: "",
      todos: [],
    });

    const addTodo = () => {
      if (state.newTodo.trim()) {
        state.todos.push({ title: state.newTodo });
        state.newTodo = "";
      }
    };
    const removeTodo = (index) => {
      state.todos.splice(index, 1);
    };
    const completeTodo = (index) => {
      state.todos[index].completed = !state.todos[index].completed;
    };

    return {
      ...toRefs(state),
      addTodo,
      removeTodo,
      completeTodo,
    };
  },
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 50px;
  height: 100vh;
  width: 100vw;
  color: rgb(33, 64, 94);
}
.title {
  font-size: 40px;
  font-weight: 600;
  margin-bottom: 40px;
}
input {
  width: 250px;
  height: 20px;
  border-radius: 5px;
  padding: 5px;
  outline: none;
  font-size: 15px;
  font-weight: bold;
}
.todo-box {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
  margin-top: 20px;
}
button {
  background-color: rgb(48, 130, 224);
  color: white;
  padding: 7px;
  width: 100px;
  margin-left: 5px;
  border: none;
  outline: none;
  cursor: pointer;
  font-size: 15px;
  font-weight: bold;
}
.completed {
  text-decoration: line-through;
  color: red;
}
</style>
