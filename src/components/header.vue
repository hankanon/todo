<template>
<header class="header">
	<h1>todos</h1>
	<input class="new-todo" autofocus autocomplete="off" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTodo">
</header>
</template>

<script>
import { mapActions } from 'vuex'
export default {
	data() {
		return {
			newTodo: ''
		}
	},
	methods: {
		addTodo() {
			this.$store.dispatch('addTodo', {
				title: this.newTodo,
				complated: false,
			})
			this.newTodo = ''
		}
	},
}
</script>

