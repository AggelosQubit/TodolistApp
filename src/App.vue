<script setup>
	import {ref , onMounted, computed, watch } from 'vue'

	const status_Array = [ 'Todo', 'Acknoledged','In Progress', 'Done', 'Cancelled'  ] 

	const todos	= ref([])
	const name	= ref('')

	const input_content 	= ref('')
	const input_category	= ref(null)

	const todos_asc = computed( ()=> todos.value.sort( (a, b)=>{
		return b.createdAt - a.createdAt
	}))

	watch(todos,newVal=>{
		localStorage.setItem('todos',JSON.stringify(newVal));
	}, {deep : true} )
	watch(name,(newVal)=>{
		console.log(newVal);
		localStorage.setItem('name',newVal);
	})

	onMounted( ()=>{
		name.value 	= localStorage.getItem('name') || '',
		todos.value = JSON.parse(localStorage.getItem('todos') ) || []
	})

	const addTodo = () => {
		if(input_content.value.trim()=== '' || input_category.value === null ) return 

		todos.value.push( {
			content : input_content.value,
			category: input_category.value,
			todo_status: status_Array[0],
			createdAt : new Date().getTime()
		} )
		console.log("IN ADDTODO");

		input_content.value='';
		input_category.value=null
	}

	const removeTodo = (todo) => {
		todos.value=todos.value.filter( (todoArr) => {return todo !== todoArr });
		console.table(todos.value)
		console.log("IN REMOVETODO");
	}
</script>

<template>
	<main class="app">
			<section class="greeting">
				<h2 class="title">
					What's up, <input type="text" placeholder="Name Here" v-model="name"/>
				</h2>

				<section class="create-todo">
					<h3>CREATE A TODO</h3>
					<form @submit.prevent="addTodo" >
						<h4>What's On Your TodoList?</h4>
						<input 
							type="text" placeholder="e.g.make a video" 
							v-model="input_content" name="" id=""/>
						
						<h4>Pick a Category</h4>

						<div class="options">
							<label>
								<input type="radio" name="category" value="business" v-model="input_category"/>
								<span class="bubble business"></span>
								<div>Business</div>
							</label>

							<label>
								<input type="radio" name="category" value="personal" v-model="input_category"/>
								<span class="bubble personal"></span>
								<div>Personal</div>
							</label>
						</div>
						
						<input type="submit" value="Add Todo">
					</form>
				</section>

			</section>
			<section class="todo-list">
				<h3>Todo List</h3>
				<div class="list">
					<div v-for="todo in todos_asc" :class="`todo-item ${ (todo.todo_status==='Done' || todo.todo_status==='Cancelled') && 'done'}`">
						<div class="todo-content">
							<select v-if="todo.category==='business'" v-model="todo.category" class="business">
								<option class="business" value="business" selected>Business</option>
								<option class="personal" value="personal">Personal</option>
							</select>
							<select v-else v-model="todo.category" class="personal">
								<option class="business" value="business" >Business</option>
								<option class="personal" value="personal" selected>Personal</option>
							</select>
						</div>
						<div class="todo-content">
							<input type="text" v-model="todo.content" />	
						</div>
						<label for="status_Array">Status of the Task : </label>
						<select class="todo-content" v-model="todo.todo_status" >
							<option  v-for="status in status_Array" >{{status}}</option>
						</select>

						<div class="actions ">
							<button class="delete" @click="removeTodo(todo)" >Delete Task</button>
						</div>
					</div> 
				</div>
			</section>
	</main>
</template>

