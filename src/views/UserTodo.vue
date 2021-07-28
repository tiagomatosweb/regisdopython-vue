<template>
    <div class="about">
        <div class="d-flex justify-content-between">
            <div>
                <h1>{{ user.name }}</h1>
                <h6>{{ user.email }}</h6>
            </div>

            <div>
                <router-link :to="{ name: 'home'}">Voltar</router-link>
            </div>
        </div>

        <UserTodoForm
            :todo="toBeUpdated"
            :user-id="userId"
            @save="onSave"
            @update="onUpdate"
        />

        <div v-if="user.todo && !user.todos.length">
            Você não possui nenhuma todo!
        </div>

        <div
            v-for="todo in user.todos"
            :key="todo.id"
            class="card mb-3"
        >
            <div class="card-body">
                <div class="d-flex justify-content-between">
                    <div>
                        <h6 class="mb-0">{{ todo.title }}</h6>
                        <small class="text-muted">{{ todo.description }}</small>
                        <div>
                            <small>
                                <a
                                    href=""
                                    class="text-primary"
                                    @click.stop.prevent="toBeUpdated = todo"
                                >
                                    Update
                                </a>
                            </small>

                            |

                            <small>
                                <a
                                    href=""
                                    class="text-danger"
                                    @click.stop.prevent="deleteTodo(todo.id)"
                                >
                                    Delete
                                </a>
                            </small>
                        </div>
                    </div>

                    <div>
                        <a
                            href=""
                            @click.stop.prevent="toggleDone(todo)"
                        >
                            <i
                                v-if="todo.is_done"
                                class="far fa-check-square"
                            ></i>

                            <i
                                v-else
                                class="far fa-square"
                            ></i>
                        </a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import UserTodoForm from '../components/UserTodoForm';
export default {
    components: { UserTodoForm },
    data() {
        return {
            user: {},
            toBeUpdated: {},
        };
    },

    computed: {
        userId() {
            return this.$route.params.id;
        },
    },

    mounted() {
        const userId = this.$route.params.id;
        fetch(`http://127.0.0.1:8000/api/users/${userId}`)
            .then(response => response.json())
            .then(res => this.user = res.data);
    },

    methods: {
        onSave(todo) {
            this.user.todos.unshift(todo);
        },

        onUpdate(todo) {
            const todos = this.user.todos;
            const idx = todos.findIndex(o => o.id === todo.id);
            todos.splice(idx, 1,todo);
        },

        toggleDone(todo) {
            const url =  todo.is_done ? 'undone' : `done`;

            fetch(`http://127.0.0.1:8000/api/todos/${todo.id}/${url}`,
                {
                    method: 'PUT',
                    headers: {
                        'Content-Type': 'application/json',
                        'Accept': 'application/json',
                    },
                })
                .then(response => response.json())
                .then((res) => {
                    todo.is_done = res.data.is_done;
                });
        },

        deleteTodo(todoId) {
            fetch(`http://127.0.0.1:8000/api/todos/${todoId}`,
                {
                    method: 'DELETE',
                    headers: {
                        'Content-Type': 'application/json',
                        'Accept': 'application/json',
                    },
                })
                .then(() => {
                    const todos = this.user.todos;
                    const idx = todos.findIndex(o => o.id === todoId);
                    todos.splice(idx, 1);
                });
        },
    },

};
</script>
