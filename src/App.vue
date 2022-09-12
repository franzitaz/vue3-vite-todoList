<script setup>
//ref: used handle the state.
//onMounted: used when we first render the page or render the component.
//computed: it will order the array by the date it was created.
//watch: will watch any changes in our references and update it.
import { ref, onMounted, computed, watch } from "vue";

const todoList = ref([]);
const name = ref("");
const inputContent = ref("");
const inputCategory = ref(null);

const todoListAscending = computed(() =>
  todoList.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

watch(name, newVal => {
  localStorage.setItem("name", newVal);
});

watch(
  todoList,
  newVal => {
    localStorage.setItem("todoList", JSON.stringify(newVal));
  },
  { deep: true }
);

const addTodo = () => {
  if (inputContent.value.trim() === "" || inputCategory.value === null) {
    return;
  }

  todoList.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  inputContent.value = "";
  inputCategory.value = null;
};

const removeTodo = todo => {
  todoList.value = todoList.value.filter(t => t !== todo);
};

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todoList.value = JSON.parse(localStorage.getItem("todoList")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello, <input type="text" placeholder="Your Name" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO!</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          placeholder="e.g. Make a video"
          v-model="inputContent"
        />

        <h4>Pick a category:</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="inputCategory"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="inputCategory"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add Todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div
          v-for="todo in todoListAscending"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
