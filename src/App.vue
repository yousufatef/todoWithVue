<template>
  <div class="container">
    <div class="form">
      <input type="text" class="input" v-model="newTodo" />
      <button class="add" @click="createNewTodo">Add Task</button>
    </div>
    <div class="tasks">
      <div class="searchAndFiltering">
        <div class="search">
          <input type="text" placeholder="Search..." v-model="searchQuery" />
        </div>
        <div class="filtering">
          <select v-model="filterStatus">
            <option value="all">All</option>
            <option value="completed">Completed</option>
            <option value="inProgress">In Progress</option>
          </select>
        </div>
      </div>
      <div class="task" v-for="(todo, index) in filteredTodos" :key="index">
        <h3 :class="{ completed: todo.completed }">{{ todo.title }}</h3>
        <div>
          <button @click="deleteTodo(index)">Delete</button>
          <button>Update</button>
          <button @click="completeTodo(index)">
            {{ todo.completed ? "Undo" : "Complete" }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive, toRefs, computed, onMounted, watch } from "vue";

export default {
  name: "App",
  setup() {
    const state = reactive({
      newTodo: "",
      todosList: [],
      filterStatus: "all",
      searchQuery: "",
    });

    const loadTodos = () => {
      try {
        const storedTodos = JSON.parse(localStorage.getItem("todos"));
        if (Array.isArray(storedTodos)) {
          state.todosList = storedTodos;
        }
      } catch (e) {
        console.error("Failed to parse todos from local storage:", e);
      }
    };

    const saveTodos = () => {
      localStorage.setItem("todos", JSON.stringify(state.todosList));
    };

    const createNewTodo = () => {
      if (state.newTodo.trim()) {
        state.todosList.push({ title: state.newTodo, completed: false });
        saveTodos();
      }
      state.newTodo = "";
    };

    const deleteTodo = (index) => {
      state.todosList.splice(index, 1);
      saveTodos();
    };

    const completeTodo = (index) => {
      state.todosList[index].completed = !state.todosList[index].completed;
      saveTodos();
    };

    const filteredTodos = computed(() => {
      let filtered = state.todosList;

      if (state.filterStatus === "completed") {
        filtered = filtered.filter((todo) => todo.completed);
      } else if (state.filterStatus === "inProgress") {
        filtered = filtered.filter((todo) => !todo.completed);
      }
      if (state.searchQuery) {
        filtered = filtered.filter((todo) =>
          todo.title.toLowerCase().includes(state.searchQuery.toLowerCase())
        );
      }

      return filtered;
    });

    // Load todos when component is mounted
    onMounted(() => {
      loadTodos();
    });

    // Watch for changes in todosList to save to local storage
    watch(() => state.todosList, saveTodos, { deep: true });

    return {
      ...toRefs(state),
      createNewTodo,
      deleteTodo,
      completeTodo,
      filteredTodos,
    };
  },
};
</script>

<style>
body {
  font-family: Arial, Helvetica, sans-serif;
  background-color: #aaa;
}

.container {
  width: 500px;
  margin: 50px auto;
}
.form {
  background-color: #eee;
  border-radius: 6px;
  padding: 20px;
  margin: 10px;
  display: flex;
  text-align: center;
}
.input {
  padding: 10px;
  flex: 1;
  border-radius: 6px;
  border: 1px solid #ddd;
  outline: none;
}
.input:focus {
  outline: none;
}
.add {
  background-color: #097c05a6;
  color: white;
  border: 1px solid #ddd;
  border-radius: 6px;
  margin-left: 10px;
  padding: 10px;
  cursor: pointer;
}
.tasks {
  background-color: #eee;
  margin: 9px;
  padding: 20px;
  border-radius: 6px;
}
.searchAndFiltering {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
}
.search input {
  width: 230px;
  padding: 10px;
  flex: 1;
  border-radius: 6px;
  outline: none;
  border: 1px solid #ddd;
}
.filtering select {
  width: 120px;
  padding: 10px;
  border-radius: 6px;
  border: 1px solid #ddd;
  outline: none;
}
.tasks .task {
  background-color: white;
  padding: 10px;
  border-radius: 6px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: 0.3s;
  border: 1px solid #ccc;
}
.tasks .task:not(:last-child) {
  margin-bottom: 15px;
}
.tasks .task:hover {
  background-color: #f7f7f7;
}
.tasks .task button {
  background-color: rgb(3, 77, 161);
  padding: 9px;
  margin-left: 2px;
  color: white;
  font-size: 13px;
  border-radius: 6px;
  font-weight: bold;
  border: none;
  cursor: pointer;
}
.completed {
  text-decoration: line-through;
  color: red;
}
</style>
