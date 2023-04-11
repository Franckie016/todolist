<template>
    <div class="container">
        <header class="header">
            <h1 class="header__title">Todos</h1>
            <input type="text" class="header__new-task" v-model="newTask" @keyup.enter="addTodo" placeholder="What needs to be done?" />
        </header>
        <section class="main" v-show="filteredTodos.length && hasTodos">
            <input type="checkbox" class="toggle-all" id="checkAll" v-model="allDone" /><label for="checkAll"></label>
            <ul class="task-list">
                <li class="task" v-for="(task, id) in filteredTodos" :key="id" :class="{ editing: task === editing }">
                    <div class="view">
                        <input class="toggle" type="checkbox" v-model="task.completed" />
                        <label :class="{ completed: task.completed }" @dblclick="editTodo(task)" @touchstart.prevent="editTodo(task)">{{ task.name }}</label>
                        <button class="destroy" @click.prevent="removeTodo(task)"></button>
                    </div>
                    <input type="text" class="edit" v-model="task.name" @keyup.enter="doneEdit" @blur="doneEdit" @keyup.esc="doneEdit" />
                </li>
            </ul>
        </section>
        <footer class="footer" v-show="hasTodos">
            <span class="task-count">
                <strong>{{ remaining }} {{ remaining > 1 ? "items" : "item" }}</strong></span
            >
            <ul class="footer__filters">
                <li class="footer__item">
                    <a class="footer__link" href="#" :class="{ selected: filter === 'all' }" @click.prevent="filter = 'all'">All</a>
                </li>
                <li class="footer__item">
                    <a class="footer__link" href="#" :class="{ selected: filter === 'task' }" @click.prevent="filter = 'task'">Active</a>
                </li>
                <li class="footer__item">
                    <a class="footer__link" href="#" :class="{ selected: filter === 'done' }" @click.prevent="filter = 'done'">Completed</a>
                </li>
            </ul>
            <button class="footer__clear-completed" v-show="completed" @click.prevent="deleteCompleted">Delete completed</button>
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
        const element = document.querySelector(".editing .edit");
        element.focus();
    });
}

//Cancel edit
function doneEdit() {
    if (editing.value !== "") {
        editing.value = null;
    } else {
        removeTodo(editing.value);
    }
}
</script>

<style lang="scss" src="../assets/styles/styles.scss">
</style>
