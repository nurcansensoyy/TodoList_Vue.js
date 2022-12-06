<script setup>
import { VueElement } from "vue";
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const inputContent = ref("");
const inputCategory = ref(null);

const addTodo = () => {
  if (inputContent.value.trim() === "" || inputCategory.value === null) {
    return;
  }
  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime(),
  });
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

watch(
  todos,
  (newValue) => {
    localStorage.setItem("todos", JSON.stringify(newValue));
  },
  { deep: true }
);

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || []);
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Naber,
        <input type="text" placeholder="İsim giriniz." v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Yapılacak İşler</h3>
      <form @submit.prevent="addTodo">
        <h4>Yapılacaklar listenizde neler var?</h4>
        <input
          type="text"
          placeholder="örneğin; video çek"
          v-model="inputContent"
        />
        <h4>Bir kategori şeçiniz.</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="inputCategory"
            />
            <span class="bubble business"></span>
            <div>İşle İlgili</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="inputCategory"
            />
            <span class="bubble personal"></span>
            <div>Kişisel</div>
          </label>
        </div>
        <input type="submit" value="Ekle" />
      </form>
    </section>
    <section class="todo-list">
      <h3>Yapılacaklar Listesi</h3>
      <div class="list">
        <div
          v-for="todo in todos_asc"
          :key="todo"
          :class="'todo-item ${todo.done}'"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="'bubble ${todo.category}'"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Sil</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
