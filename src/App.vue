<script setup>
import {ref, onMounted, computed, watch} from "vue";
import {data} from "autoprefixer";


const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

//fonction pour trier le tableau de données du plus récent au plus ancien
const todoAsc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const firstLetterCapitalizer = (string) => {
  return string.charAt(0).toUpperCase() + string.slice(1);
}


onMounted(() => {
  name.value = localStorage.getItem('name') || ""
  todos.value = JSON.parse(localStorage.getItem('todos')) || []

})

//Ajouter une tache à faire dans le tableau avec les données saisis
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
    createdDate: new Date().toLocaleDateString()
  })

  input_content.value = ''
  input_category.value = null

}

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})

//supprimer  une tache réalisée
const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

const remaining = computed(() => {
  return todos.value.filter(todo => !todo.done).length + " "
})


</script>

<template>
  <main class=" flex justify-center md:min-h-screen md:items-center 	 ">

    <div class="w-96">

      <div class="py-5">
        <h2 class="text-2xl font-semibold "> Bonjour, <input type="text" placeholder="Monsieur" v-model="name"
                                                             class="outline-none w-28 text-gray-400 font-normal bg-[#f9f9f9] ">
        </h2>
      </div>

      <div>
        <h2 class="pb-1 font-medium text-lg">Créer une note</h2>

        <form @submit.prevent="addTodo">
          <div class="grid inline-grid w-full py-2">
            <span>Qu'est-ce qu'il y'a à faire</span>
            <input type="text" placeholder="Faire mes devoirs..." v-model="input_content"
                   class="bg-neutral-100 outline-none p-2 "/>
          </div>

          <div>
            <h3 class="text-base font-medium py-2">Catégorie</h3>

            <div class="flex  justify-between gap-5 ">

              <div class="w-full relative">
                <input class="checkbox-input absolute cursor-pointer opacity-0 w-full  h-20" type="radio"
                       value="Business" name="categorie" id="" v-model="input_category"/>
                <div
                    class="bg-red-400 border rounded-lg  duration-500 ease-in-out h-20 flex justify-center items-center ">
                  <h4 class="text-white">Business</h4>
                </div>
              </div>

              <div class="w-full relative">
                <input class="checkbox-input absolute cursor-pointer opacity-0 w-full  h-20" type="radio"
                       value="Personnel" name="categorie"
                       id="" v-model="input_category"/>
                <div
                    class="bg-blue-500 border rounded-lg duration-500 ease-in-out  h-20 flex justify-center items-center">
                  <h4 class="text-white">Personnel</h4>
                </div>
              </div>

            </div>

          </div>


          <button type="submit" value="ajouter"
                  class="p-3 my-5 w-full text-white font-semibold shadow-xl rounded-lg bg-neutral-900">Ajouter
          </button>

        </form>

      </div>

      <div>
        <div class="flex items-center gap-3">
          <h2 class=""> A FAIRE</h2>

          <h3 class="text-sm text-gray-400 ">{{ remaining }}Tache(s)</h3>
        </div>


        <div class="py-2 ml-3 overflow-scroll h-72 ">
          <div v-for="todo in todoAsc" class="py-1 text-lg relative "
               :class="[todo.done? 'line-through opacity-50' : '']">
            <div class="bg-white shadow rounded-sm w-full p-2 flex items-center  gap-2 ">
              <label>
                <input class="cursor-pointer" type="checkbox" v-model="todo.done">
              </label>
              <span :class="[todo.category === 'Business' ? 'text-red-500' : 'text-blue-500' ]">{{
                  firstLetterCapitalizer(todo.content)
                }}
              </span>

              <button class="cursor-pointer absolute right-2" @click="removeTodo(todo)">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24px" height="24px">
                  <path d="M17,5l-3-3h-4L7,5H17z"/>
                  <line x1="4" x2="20" y1="4" y2="4" fill="none" stroke="#000" stroke-miterlimit="10" stroke-width="2"/>
                  <line x1="14" x2="14" y1="18" y2="7" fill="none" stroke="#000" stroke-miterlimit="10"
                        stroke-width="2"/>
                  <line x1="10" x2="10" y1="18" y2="7" fill="none" stroke="#000" stroke-miterlimit="10"
                        stroke-width="2"/>
                  <path fill="none" stroke="#000" stroke-miterlimit="10" stroke-width="2"
                        d="M6,4v16c0,0.6,0.4,1,1,1h10c0.6,0,1-0.4,1-1V4"/>
                </svg>
              </button>

            </div>
          </div>
        </div>
      </div>


    </div>
  </main>

</template>


