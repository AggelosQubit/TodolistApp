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

	const addTodo = ()=> {
		if(input_content.value.trim()=== '' || input_category.value === null ) return 

		todos.value.push( {
			content : input_content.value,
			category: input_category.value,
			todo_status: status_Array[0],
			createdAt : new Date().getTime()
		} )
		console.log("IN ADDTODO");
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
					<div v-for="todo in todos_asc" :class="`todo-item ${ (todo.done==='Done') && 'done'}`">
						<label v-if="todo.category==='business'" class=business>
							{{ todo.category.split("")[0].toUpperCase() +"" + todo.category.substr(1,todo.category.length)  }}
						</label>
						<label v-else class="personal">
							{{ todo.category.split("")[0].toUpperCase() +"" + todo.category.substr(1,todo.category.length)  }}
						</label>
						<label for="status_Array">Status of the Task : </label>
						<select v-model="todo.todo_status" >
							<option  v-for="status in status_Array" >{{status}}</option>
						</select>
					</div> 
				</div>
			</section>
	</main>
</template>

