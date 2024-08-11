<template>
  <div class="wrapper" :class="theme">
    <div class="appBlock">
      <corner-component></corner-component>
      <div class="themeBlock">
        <button
          class="defaultThemeButton"
          @click="setTheme('defaultTheme')"
        ></button>
        <button
          class="lightThemeButton"
          @click="setTheme('lightTheme')"
        ></button>
        <button class="darkThemeButton" @click="setTheme('darkTheme')"></button>
      </div>
      <div class="titleBlock">
        <h1>Just do it.</h1>
      </div>
      <div class="inputBlock">
        <input
          id="addInput"
          type="text"
          placeholder="Add a task..."
          v-model.trim="todo"
          @keydown.enter="addTodo"
          autocomplete="off"
        />
        <button id="addButton" @click="addTodo">I Got This</button>
      </div>
      <div class="errorBlock">
        <div class="error" v-show="emptyFieldError">
          Empty tasks not allowed
        </div>
        <div class="error" v-show="maxLengthError">Max length 40 letters</div>
        <div class="error" v-show="minLengthError">Min length 4 letters</div>
        <div class="error" v-show="maxCount">Max tasks count</div>
      </div>
      <div class="infoBlock">
        <span>
          Total Tasks:
          {{ todosLength }}
        </span>
        <span> {{ time }} </span>
        <span>Completed Tasks : {{ doneTodosLength }} </span>
      </div>
      <ul class="todosList">
        <li v-for="(todo, index) in todos" :key="index">
          <div
            class="todoBlock"
            :class="{ done: todo.isDone, drop: todo.isDrop }"
          >
            <span>
              {{ todo.todo }}
            </span>
            <div class="options">
              <button @click="toggleDone(todo.id)">
                <i class="fas fa-check"></i></button
              ><button @click="remove(todo.id)">
                <i class="fas fa-trash"></i>
              </button>
            </div>
          </div>
        </li>
      </ul>
      <div class="footer">Created by Stetsnko Kirill</div>
    </div>
  </div>
</template>

<script>
import CornerComponent from "./CornerComponent.vue";
import { v4 } from "uuid";
export default {
  name: "App",

  mounted() {
    if (localStorage.getItem("todos")) {
      this.todos = JSON.parse(localStorage.getItem("todos"));
    }

    this.todos.map((todo) => (todo.isDrop = false));
  },

  data() {
    return {
      theme: "defaultTheme",
      time: new Date().toLocaleString(),
      todo: "",
      maxLengthError: false,
      minLengthError: false,
      emptyFieldError: false,
      maxCount: false,

      todos: [],
    };
  },

  computed: {
    todosLength() {
      return this.todos.length;
    },

    doneTodosLength() {
      let counter = 0;
      this.todos.map((todo) => (todo.isDone ? counter++ : null));
      return counter;
    },
  },

  methods: {
    setTheme(theme) {
      this.theme = theme;
    },

    addTodo() {
      const length = this.todo.length;

      this.emptyFieldError = !this.todo;
      this.maxCount = this.todosLength >= 20;
      this.minLengthError = length > 0 && length < 4;
      this.maxLengthError = length > 40;

      if (
        !this.emptyFieldError &&
        !this.minLengthError &&
        !this.maxLengthError &&
        !this.maxCount
      ) {
        this.todos.push({
          id: v4(),
          todo: this.todo,
          isDone: false,
        });
      }
      this.setItem();
      this.todo = "";
    },

    toggleDone(id) {
      let elem = this.todos.find((todo) => todo.id === id);
      elem.isDone = !elem.isDone;
      this.setItem();
    },

    remove(id) {
      const elem = this.todos.find((todo) => todo.id == id);
      elem.isDrop = true;

      setTimeout(() => {
        this.todos = this.todos.filter((todo) => todo.id !== id);
        this.maxCount = this.todos.length >= 20;
        this.setItem();
      }, 300);
    },

    setItem() {
      localStorage.setItem("todos", JSON.stringify(this.todos));
    },

    getItem() {
      this.todos = JSON.parse(localStorage.getItem("todos"));
    },
  },

  components: { CornerComponent },
};
</script>

<style lang="scss" scoped></style>
