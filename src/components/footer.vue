<template>
<footer class="footer" v-show="todos.length">
	<span class="todo-count">
		<strong v-text="remaining"></strong> {{pluralize('item', remaining)}} left
	</span>
	<ul class="filters">
		<li><a href="#/all" :class="{selected: visibility == 'all'}">All</a></li>
		<li><a href="#/active" :class="{selected: visibility == 'active'}">Active</a></li>
		<li><a href="#/completed" :class="{selected: visibility == 'completed'}">Completed</a></li>
	</ul>
	<button class="clear-completed" @click="removeCompleted" v-show="todos.length > remaining">
		Clear completed
	</button>
</footer>
</template>

<script>
import { mapGetters } from 'vuex'
var filters = {
		all: function (todos) {
			return todos;
		},
		active: function (todos) {
			return todos.filter(function (todo) {
				return !todo.completed;
			});
		},
		completed: function (todos) {
			return todos.filter(function (todo) {
				return todo.completed;
			});
		}
	};
export default {
	computed: {
		...mapGetters([
			"visibility",
			"todos"
		]),
		remaining: function () {
			return filters.active(this.todos).length;
		},
	},
	methods:{
		changeRouter(router) {
			this.$store.dispatch('changeRouter',router)
		},
		removeCompleted() {
			this.$store.dispatch('removeCompleted')
		},
		pluralize: function (word, count) {
			return word + (count === 1 ? '' : 's');
		},
	}
}
</script>

