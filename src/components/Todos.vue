<template>
    <div class="container">
        <header>
            <h1>Todos</h1>
            <input type="text" class="new-todo" v-model="newTodo" @keyup.enter="addTodo" placeholder="What needs to be done?" />
        </header>
        <section class="main" v-show="filteredTodos.length && hasTodos">
            <input type="checkbox" class="toggle-all" id="checkAll" v-model="allDone" /><label for="checkAll"></label>
            <ul class="todo-list">
                <li class="todo" v-for="(todo, id) in filteredTodos" :key="id" :class="{ editing: todo === editing }">
                    <div class="view">
                        <input class="toggle" type="checkbox" v-model="todo.completed" />
                        <label :class="{ completed: todo.completed }" @dblclick="editTodo(todo)">{{ todo.name }}</label>
                        <button class="destroy" @click.prevent="removeTodo(todo)"></button>
                    </div>
                    <input type="text" class="edit" v-model="todo.name" @keyup.enter="doneEdit" />
                </li>
            </ul>
        </section>
        <footer class="footer" v-show="hasTodos">
            <span class="todo-count">
                <strong>{{ remaining }} {{ remaining > 1 ? "items" : "item" }}</strong></span
            >
            <ul class="filters">
                <li><a href="#" :class="{ selected: filter === 'all' }" @click.prevent="filter = 'all'">All</a></li>
                <li><a href="#" :class="{ selected: filter === 'todo' }" @click.prevent="filter = 'todo'">Active</a></li>
                <li><a href="#" :class="{ selected: filter === 'done' }" @click.prevent="filter = 'done'">Completed</a></li>
            </ul>
            <button class="clear-completed" v-show="completed" @click.prevent="deleteCompleted">Delete completed</button>
        </footer>
    </div>
</template>

<script setup>
import { reactive, computed, ref } from "vue";
//State
const newTodo = ref("");
const todos = reactive([
    {
        name: "",
        completed: false,
    },
]);
const filter = ref("all");
const editing = ref(null);

//Ajouter une nouvelle tâche
function addTodo() {
    if (newTodo.value !== "") {
        todos.push({
            name: newTodo.value,
            completed: false,
        });
    }
    newTodo.value = "";
}

//supprimer une tâche
function removeTodo(todo) {
    const index = todos.findIndex((t) => t.name === todo.name);
    if (index !== -1) {
        todos.splice(index, 1);
    }
}

//supprimer les tâches qui sont faites
function deleteCompleted() {
    todos.filter((todo) => todo.completed).forEach((todo) => removeTodo(todo));
}

//Editer une tâche
function editTodo(todo) {
    editing.value = todo;
}

function doneEdit() {
    editing.value = null;
}

const remaining = computed(() => {
    return todos.filter((todo) => !todo.completed && todo.name !== "").length;
});

const completed = computed(() => {
    return todos.filter((todo) => todo.completed).length;
});

//filtrer les tâches par categeories (tout/faite/ à faires)
let filteredTodos = computed(() => {
    if (filter.value === "todo") {
        return todos.filter((todo) => !todo.completed);
    } else if (filter.value === "done") {
        return todos.filter((todo) => todo.completed);
    }
    return todos.filter(todo => todo !== todos[0]);
});

//Marquer tout les tâches comme faites ou  comme à faire
const allDone = computed({
    get() {
        return remaining === 0;
    },

    set(value) {
        todos.forEach((todo) => {
            todo.completed = value;
        });
    },
});

const hasTodos = computed(() => {
    if ((todos.length -1 )> 0 ) {
        return todos.length > 0;
    }
});
</script>

<style scoped>
.container {
    color: rgb(141, 153, 174);
    font-family: sans-serif;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin-top: 40px;
}

.container h1 {
    font-size: 100px;
    text-align: center;
    color: rgb(217, 4, 41, 0.5);
}

.new-todo,
.edit {
    position: relative;
    color: #333;
    background-color: #edf2f4;
    font-size: 32px;
    line-height: 1.4em;
    padding: 16px 16px 16px 70px;
    width: 540px;
    border: none;
    box-shadow: rgb(109, 118, 134) 0px 3px 10px 0px;
}

.edit {
    display: none;
}

/*.edit {
	position: relative;
	margin: 0;
	width: 100%;
	font-size: 24px;
	font-family: inherit;
	font-weight: inherit;
	line-height: 1.4em;
	border: 0;
	color: inherit;
	padding: 6px;
	border: 1px solid #999;
	box-shadow: inset 0 -1px 5px 0 rgba(0, 0, 0, 0.2);
	box-sizing: border-box;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}
*/
.new-todo[placeholder]:placeholder-shown {
    opacity: 0.2;
}

.new-todo:focus {
    outline: none;
}
.completed {
    color: rgba(141, 153, 174, 0.5);
    text-decoration: line-through;
}

.main {
    box-shadow: rgba(141, 153, 174, 0.8) 0px 4px 2px 0px;
    padding: 0 20px;
}

.main > div:first-child {
    margin-top: 10px;
}

#checkAll {
    position: absolute;
    opacity: 0;
    width: 0;
}

#checkAll + label {
    display: inline-block;
    font-size: 3rem;
    cursor: pointer;
    position: relative;
    bottom: 75px;
}

#checkAll + label::before {
    content: "\25BC"; /* code pour la flèche vers le bas */
    display: inline-block;
    font-size: 0.8em;
    transform: translateY(2px);
}

.todo-list {
    font-size: 32px;
    margin-top: 10px;
}

.editing {
    border-bottom: none;
    padding: 0;
}

.editing .edit {
    display: block;
    width: 506px;
    padding: 12px 16px;
    margin: 0 0 0 43px;
}
.editing .view {
    display: none;
}

.todo {
    padding-bottom: 15px;
    /*border-bottom: 1px solid rgb(141, 153, 174, .5);*/
}
.view {
    width: 500px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.view *,
.footer * {
    display: block;
}

.toggle {
    /* Masque l'icône de base */
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    /* Définit la taille du cercle */
    width: 40px;
    height: 40px;
    /* Définit la bordure et la couleur de fond */
    border: 2px solid #8d99ae;
    border-radius: 50%;
    background-color: inherit;
    opacity: 0.2;
    text-decoration: none;
    outline: none;
}

.toggle:checked::before {
    content: "\2714"; /* Code ASCII de l'icône check */
    display: block;
    text-align: center;
    font-size: 35px;
    line-height: 40px;
    color: #0f9d58; /* Couleur verte */
}
.toggle:checked {
    opacity: 0.6;
}

.destroy {
    position: relative;
    display: inline-block;
    width: 50px;
    height: 50px;
    border: none;
    cursor: pointer;
}

/* Créer les deux lignes diagonales qui forment la croix */
.destroy::before,
.destroy::after {
    content: "";
    position: absolute;
    top: 50%;
    left: 50%;
    width: 25px;
    height: 2px;
    background-color: #d90429;
    transform: translate(-50%, -50%) rotate(45deg);
    opacity: 0.1;
}

/* Positionner la première ligne diagonale */
.destroy::before {
    transform: translate(-50%, -50%) rotate(-45deg);
}

/* Ajouter un effet de survol sur le bouton */
.destroy:hover::before,
.destroy:hover::after {
    opacity: 0.5;
}

.footer {
    width: 540px;
    padding: 17px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.footer .filters {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.footer .filters li {
    display: inline-block;
}

.footer .filters li a {
    outline: none;
    text-decoration: none;
    color: inherit;
    margin: 5px;
    padding: 3px 7px;
}

.selected {
    border: 1px solid rgba(141, 153, 174, 0.5);
}
</style>
