<template>
  <div id="app">
    <section class="todoapp" v-cloak>
			<v-header></v-header>
			<router-view/>
			<v-footer></v-footer>
		</section>
  </div>
</template>
<script>
import vHeader from 'components/header'
import vFooter from 'components/footer'
	var STORAGE_KEY = 'todos-vuejs';

	window.todoStorage = {
		fetch: function () {
			return JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
		},
		save: function (todos) {
			localStorage.setItem(STORAGE_KEY, JSON.stringify(todos));
		}
	};
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
    data(){
      return {
          todos: todoStorage.fetch(),
          newTodo: '',
          editedTodo: null,
          visibility: 'all'
        }
    },
    components: {
      vHeader,
      vFooter
    },
    computed: {
			filteredTodos: function () {
				return filters[this.visibility](this.todos);
			},
			remaining: function () {
				return filters.active(this.todos).length;
			},
			allDone: {
				get: function () {
					return this.remaining === 0;
				},
				set: function (value) {
					this.todos.forEach(function (todo) {
						todo.completed = value;
					});
				}
			}
		},
    watch: {
			todos: {
				deep: true,
				handler: todoStorage.save
			}
    },
    directives: {
			'todo-focus': function (el, binding) {
				if (binding.value) {
					el.focus();
				}
			}
		},
    methods: {

			pluralize: function (word, count) {
				return word + (count === 1 ? '' : 's');
			},

			addTodo: function () {
				var value = this.newTodo && this.newTodo.trim();
				if (!value) {
					return;
				}
				this.todos.push({ id: this.todos.length + 1, title: value, completed: false });
				this.newTodo = '';
			},

			removeTodo: function (todo) {
				var index = this.todos.indexOf(todo);
				this.todos.splice(index, 1);
			},

			editTodo: function (todo) {
				this.beforeEditCache = todo.title;
				this.editedTodo = todo;
			},

			doneEdit: function (todo) {
				if (!this.editedTodo) {
					return;
				}
				this.editedTodo = null;
				todo.title = todo.title.trim();
				if (!todo.title) {
					this.removeTodo(todo);
				}
			},

			cancelEdit: function (todo) {
				this.editedTodo = null;
				todo.title = this.beforeEditCache;
			},

			removeCompleted: function () {
				this.todos = filters.active(this.todos);
			}
		},
}
</script>

<style lang="scss">
@import 'assets/css/index';
@import 'assets/css/base';
[v-cloak] { display: none; }
</style>
