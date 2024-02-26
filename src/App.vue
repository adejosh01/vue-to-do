<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')


const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '') {
		return
	}

	todos.value.push({
		content: input_content.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="mt-8 mb-2 pl-6 pr-14 ">
			<h2 class="flex text-[#313154] text-2xl font-bold borde">
				What's up, <input class="ml-2 flex-1 min-w-0 text-[#313154] text-2xl font-bold " type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
		</section>


		<section class="mt-4 mb-8 pl-6 pr-14 create-todo">
			<h3 class="text-[#313154] text-xl font-bold mb-2">CREATE A TODO</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4 class="text-[#313154] text-md font-bold mb-2">What's on your todo list?</h4>
				<input 
          class="block w-full text-xl py-4 px-6 text-[#313154] bg-white rounded-md shadow-md mb-2"
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. make a video"
					v-model="input_content" />
				
         <div class="block w-40 text-lg py-4 px-6 text-white bg-blue-500 rounded-md  cursor-pointer transition duration-200 ease-in-out">
          <input   type="submit" value="Add todo" />
         </div>
      

				
			</form>
		</section>

		<section class="mt-8 mb-8 pl-6 pr-14 todo-list">
			<h3 class="text-[#313154] text-base font-normal mb-2">TODO LIST</h3>
			<div class="m-2" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label class="block mr-2 cursor-pointer">
						<input type="checkbox" v-model="todo.done" />
						<span></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="bg-blue-400" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>
