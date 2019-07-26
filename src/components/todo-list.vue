<template>
<section class="main" v-show="todos.length">
  <input id="toggle-all" class="toggle-all" type="checkbox" @change="toggleCompleted(!allDone)" :checked="allDone">
  <label for="toggle-all">Mark all as complete</label>
  <ul class="todo-list">
    <li class="todo" v-for="(todo,index) in filteredTodos" :key="index" :class="{completed: todo.completed, editing: todo == editedTodo}">
      <div class="view">
        <input class="toggle" type="checkbox" v-model="todo.completed">
        <label @dblclick="editTodo(todo)">{{todo.title}}</label>
        <button class="destroy" @click="removeTodo(todo)"></button>
      </div>
      <input class="edit" type="text" v-model="todo.title" v-todo-focus="todo == editedTodo" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)">
    </li>
  </ul>
</section>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
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
	beforeRouteEnter (to, from, next) {
		console.log(to)
		next(vm => {
			vm.changeRouter(to.name)
		})
	},
	data() {
		return {
			editedTodo: null
		}
	},
	computed: {
		...mapGetters([
			'todos',
			'visibility',
			'allDone'
		]),
		filteredTodos() {
			return filters[this.visibility](this.todos)
		}
		
	},
	methods: {
		...mapActions([
			'addTodo',
			'removeTodo',
			'changeRouter',
		]),
		toggleCompleted(done) {
			this.$store.dispatch('toggleCompleted', done)
		},
		doneEdit(todo) {
			if (!this.editedTodo) {
				return;
			}
			this.editedTodo = null;
			todo.title = todo.title.trim();
			if (!todo.title) {
				this.removeTodo(todo);
			}
		},
		cancelEdit(todo) {
			this.editedTodo = null;
			todo.title = this.beforeEditCache;
		},
		changeRouter(router) {
			this.$store.dispatch('changeRouter',router)
		},
		editTodo(todo) {
			this.beforeEditCache = todo.title;
			this.editedTodo = todo;
		}
	},
	directives: {
		'todo-focus': function (el, binding) {
			if (binding.value) {
				el.focus();
			}
		}
	}
}
</script>

