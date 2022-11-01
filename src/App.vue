<script setup>
	import { ref, onMounted, computed, watch } from "vue";

	const todos = ref([]);
	const name = ref("lol");

	const inputContent = ref('');
	const inputCategory = ref('');
	
	const sortTodos = computed(() => todos.value.sort((a, b) => {
		return b.createdAt - a.createdAt;
	}))

	const addTodo = () => {
		if (inputContent.value.trim() === '' || !inputContent.value)
			return ;
		
		todos.value.push({
			content: inputContent.value,
			category: inputCategory.value,
			done: false,
			createdAt: Date.now()
		})

		inputContent.value = '';
		inputCategory.value = null;
	}

	const removeTodo = (todo) => {
		todos.value = todos.value.filter(t => t !== todo);
	}

	watch(todos, value => {
		localStorage.setItem('todos', JSON.stringify(value));
	}, { deep: true });

	watch(name, value => {
		localStorage.setItem('name', value);
	})

	onMounted(() => {
		name.value = localStorage.getItem('name') || '';
		todos.value = JSON.parse(localStorage.getItem('todos')) || [];
	});
</script>

<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">
				What's up, <input type="text" placeholder="Name here" v-model="name" />
			</h2>
		</section>
		<section class="create-todo">
			<h3>Create a todo</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input
					type="text"
					placeholder="e.g. Make a video"
					v-model="inputContent"
				>
				<h4>Pick a category</h4>
				<div class="options">
					<label>
						<input
							type="radio"
							name="category"
							id="category1"
							value="business"
							v-model="inputCategory"
						/>
						<span class="bubble business" />
						<span>Business</span>
					</label>

					<label>
						<input
							type="radio"
							name="category"
							id="category2"
							value="personal"
							v-model="inputCategory"
						/>
						<span class="bubble personal" />
						<span>Personal</span>
					</label>
				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>
		<section class="todo-list">
			<h3>Todo list</h3>
			<div class="list" id="todo-list">
				<div v-for="todo in sortTodos" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done">
						<span :class="`bubble ${
								todo.category == 'business' 
									? 'business' 
									: 'personal'
						}`"></span>
					</label>
	
					<div class="todo-content">
						<input type="text" v-model="todo.content">
					</div>
	
					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">
							Delete
						</button>
					</div>
				</div>
			</div>
		</section>
	</main>
</template>