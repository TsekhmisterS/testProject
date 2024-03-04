<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = "";
  input_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((item) => item !== todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

// after the component has been mounted in the DOM, get name from storage
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello,
        <input v-model="name" type="text" placeholder="type your name here" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create a todo item</h3>

      <form @submit.prevent="addTodo">
        <h4>What is on your todo list?</h4>
        <input
          type="text"
          placeholder="e.g. feed the cat"
          v-model="input_content"
        />
        <h4>Choose a category</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="work"
              v-model="input_category"
            />
            <span class="bubble work"></span>
            <div>Work</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>Todo List</h3>
      <div class="list">
        <div
          v-for="todo in todos_asc"
          :key="todo.createdAt"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>

            <div class="todo-content">
              <input type="text" v-model="todo.content" />
            </div>
          </label>
          
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
