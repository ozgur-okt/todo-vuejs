<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([]) // Create a reactive reference to an empty array of todos
const name = ref('') // Create a reactive reference to an empty string named `name`

const input_content = ref('') // Create a reactive reference to an empty string named `input_content`
const input_category = ref(null) // Create a reactive reference to a null value named `input_category`

const todos_asc = computed(() => todos.value.sort((a,b) =>{ // Create a computed property that sorts the todos array by creation time in ascending order
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => { // Set up a watcher to observe changes to `name` and save it to localStorage
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => { // Set up a watcher to observe changes to `todos` and save it to localStorage
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true // Enable deep watching so changes to nested objects in `todos` are also detected
})

const addTodo = () => { // Define a function to add a new todo
	if (input_content.value.trim() === '' || input_category.value === null) { // Ensure that input content and category are not empty
		return
	}

	todos.value.push({ // Add a new todo object to the `todos` array
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime() // Store the creation time of the todo
	})
}

const removeTodo = (todo) => { // Define a function to remove a todo from the `todos` array
	todos.value = todos.value.filter((t) => t !== todo) // Update the `todos` array to remove the specified todo
}

onMounted(() => { // Set up a lifecycle hook that runs after the component is mounted
	name.value = localStorage.getItem('name') || '' // Get the `name` value from localStorage, or use an empty string if it's not set
	todos.value = JSON.parse(localStorage.getItem('todos')) || [] // Get the `todos` value from localStorage, or use an empty array if it's not set
})

</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				What's up, <input type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3>CREATE A TODO</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. make a video"
					v-model="input_content" />
				
				<h4>Pick a category</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Business</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>

				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
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
