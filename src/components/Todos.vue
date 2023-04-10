<template>
    <div class="container">
        <header>
            <h1>Todos</h1>
            <input type="text" class="new-task" v-model="newTask" @keyup.enter="addTodo" placeholder="What needs to be done?" />
        </header>
        <section class="main" v-show="filteredTodos.length && hasTodos">
            <input type="checkbox" class="toggle-all" id="checkAll" v-model="allDone" /><label for="checkAll"></label>
            <ul class="task-list">
                <li class="task" v-for="(task, id) in filteredTodos" :key="id" :class="{ editing: task === editing }">
                    <div class="view">
                        <input class="toggle" type="checkbox" v-model="task.completed" />
                        <label :class="{ completed: task.completed }" @dblclick="editTodo(task)">{{ task.name }}</label>
                        <button class="destroy" @click.prevent="removeTodo(task)"></button>
                    </div>
                    <input type="text" class="edit" v-model="task.name" @keyup.enter="doneEdit" @blur="doneEdit"/>
                </li>
            </ul>
        </section>
        <footer class="footer" v-show="hasTodos">
            <span class="task-count">
                <strong>{{ remaining }} {{ remaining > 1 ? "items" : "item" }}</strong></span
            >
            <ul class="filters">
                <li>
                    <a href="#" :class="{ selected: filter === 'all' }" @click.prevent="filter = 'all'">All</a>
                </li>
                <li>
                    <a href="#" :class="{ selected: filter === 'task' }" @click.prevent="filter = 'task'">Active</a>
                </li>
                <li>
                    <a href="#" :class="{ selected: filter === 'done' }" @click.prevent="filter = 'done'">Completed</a>
                </li>
            </ul>
            <button class="clear-completed" v-show="completed" @click.prevent="deleteCompleted">Delete completed</button>
        </footer>
    </div>
</template>

<script setup>
import { reactive, computed, ref, nextTick } from "vue";
//Data
const newTask = ref("");
const todos = reactive([]);
const filter = ref("all");
const editing = ref(null);
const editInput = ref(null)

/*==================Computed=================*/
const remaining = computed(() => {
    return todos.filter((task) => !task.completed).length;
});

const completed = computed(() => {
    return todos.filter((task) => task.completed).length;
});

const allDone = computed({
    get() {
        return remaining === 0;
    },

    set(value) {
        todos.forEach((task) => {
            task.completed = value;
        });
    },
});

const filteredTodos = computed(() => {
    if (filter.value === "task") {
        return todos.filter((task) => !task.completed);
    } else if (filter.value === "done") {
        return todos.filter((task) => task.completed);
    }
    return todos;
});

const hasTodos = computed(() => {
    return todos.length > 0;
});

/*===================Methods=================*/
//Ajouter une nouvelle tâche
function addTodo() {
    if (newTask.value.trim() !== "") {
        todos.push({
            name: newTask.value,
            completed: false,
        });
    }
    newTask.value = "";
}

//supprimer une tâche
function removeTodo(task) {
    const index = todos.indexOf(task);
    if (index !== -1) {
        todos.splice(index, 1);
    }
}

//supprimer les tâches qui sont faites
function deleteCompleted() {
    const completedTodos = todos.filter((task) => task.completed);
    for (const task of completedTodos) {
        removeTodo(task);
    }
}

//Editer une tâche
function editTodo(task) {
    editing.value = task;
    nextTick(() => {
        const element = document.querySelector(".editing .edit")
        element.focus()

    })
}

//Cancel edit
function doneEdit() {
    if (editing.value.name.trim() !== "") {
        editing.value = null;
    } else {
        removeTodo(editing.value);
    }
}
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

.new-task,
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

.new-task[placeholder]:placeholder-shown {
    opacity: 0.2;
}

.new-task:focus {
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

.task-list {
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

.task {
    padding-bottom: 15px;
}

.task label {
    padding: 0 5px;
    max-width: 390px;
    word-wrap: break-word;
    overflow-wrap: break-word;
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

.clear-completed {
    color: inherit;
    background-color: none;
    border: none;
    font-size: inherit;
}

.clear-completed:hover {
    text-decoration: underline;
}
</style>
