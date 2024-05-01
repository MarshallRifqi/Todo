<!-- <script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import HelloWorld from './components/HelloWorld.vue'
</script> -->

<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_time = ref(null)

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
		time: input_time.value,
		done: false,
		editable: true,
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

// const editTodo = (todo) => {
// 	const newTitle = prompt('Edit Todo Title:', todo.content)
// 	const newTime = prompt('Edit Todo Time:', todo.time)
// 	if (newTitle !== null) {
// 		todo.content = newTitle.trim()
// 		todo.time = newTime.trim()
// 	}
// }

const editTodo = (todo) => {
    let newTitle, newTime;

    do {
        newTitle = prompt('Edit Todo Title:', todo.content);
    } while (newTitle === null || newTitle.trim() === "");

    do {
        newTime = prompt('Edit Todo Time:', todo.time);
    } while (newTime === null || newTime.trim() === "");

    todo.content = newTitle.trim();
    todo.time = newTime.trim();
}


// clock

//end of clock

</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				Halo,<input type="text" id="name" placeholder="Nama" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<div class="title-wrapper">
				<h3 id="todo-title">Create a To-do</h3>
			</div>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. make a Homework"
					v-model="input_content" />
					<input type="time" class="time-pickable" v-model="input_time">
				
				<!-- <h4>Pick a category</h4>
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

				</div> -->

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<div class="title-wrapper">
				<h3>List of Your To-Do</h3>
			</div>
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
						<input type="text" v-model="todo.content" readonly />
						<input type="text" v-model="todo.time" readonly style="color: #888;"/>
					</div>

					<div class="actions">
						<button class="update" @click="editTodo(todo)">Edit</button>
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>
