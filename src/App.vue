<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createAt - a.createAt;
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t  => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem("todos", JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value =  JSON.parse(localStorage.getItem('todos')) || [];
})
</script>

<template>
  <main class="app">
    <div class="w-full h-screen container mx-auto ">
      <section class=" text-center p-8 border rounded-full mb-3 bg-emerald-400 ">
        <h1 class="text-3xl font-bold ">
          Jakie zadanko wariacie!
        </h1>
        <input class="w-full text-center text-3xl bg-emerald-400" type="text" placeholder="Podaj Imię" v-model="name"/>
      </section>

      <section class="text-center p-8 border rounded-full mb-3 bg-emerald-400 ">
        <h2 class="text-2xl font-medium">Dajesz zadanko wariacie!</h2>

        <form @submit.prevent="addTodo">
          <h4 class="text-xl font-medium">Lecisz po swoje</h4>
          <input 
          class="mt-3 text-center rounded-lg bg-gray-900 text-white"
          type="text" 
          placeholder="Do roboty!" 
          v-model="input_content"/>
          
          <h4 class="mt-3 text-lg font-medium">Wybierz Kategorie!</h4>

          <div class="flex gap-6 justify-center mt-3">
            <label class="p-4 border bg-gray-900 rounded-full text-white hover:bg-sky-700 ">
              <input 
              type="radio"
              name="category"
              value="business"
              v-model="input_category"
              />
              <div>Business</div>
            </label>

            <label class="p-4 border bg-gray-900 rounded-full text-white hover:bg-sky-700 ">
              <input 
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
              /> 
              <div>Personal</div>
            </label>
          </div>

          <input 
          class="text-xl font-medium border rounded-full bg-gray-900 text-white p-3  hover:bg-sky-700"
          type="submit" value="Add todo">
        </form>
      </section>

      <section  class="text-center p-8 border rounded-3xl lg:rounded-full mb-3 bg-emerald-400">
        <h3 class="text-2xl font-medium">Do  roboty!</h3>
        <div>

          <div class="flex items-center justify-center"
          v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

            <label >
              <input
              class=""
              type="checkbox" v-model="todo.done"/>
            </label>

            <div>
              <input
              class="rounded-full bg-emerald-400 text-xl font-medium ml-5"
              type="text" v-model="todo.content">
            </div>

            <div>
              <button 
              class="text-xl font-medium border rounded-full bg-gray-900 text-white p-3  hover:bg-sky-700"
              @click="removeTodo(todo)" >
                Delete
              </button>
            </div>

          </div>

        </div>
      </section>
    </div>
  </main>
</template>


