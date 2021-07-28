<template>
    <div class="my-5">
        <form @submit.stop.prevent="submit">
            <div class="form-group mb-2">
                <label for="title">Titulo</label>
                <input
                    v-model="title"
                    id="title"
                    type="text"
                    class="form-control"
                >
            </div>

            <div class="form-group">
                <label for="drescription">Descrição</label>
                <textarea
                    v-model="description"
                    id="drescription"
                    class="form-control"
                ></textarea>
            </div>

            <div class="row">
                <div class="col">
                    <div class="form-group mb-2">
                        <label for="date">Data</label>
                        <input
                            v-model="dueDate"
                            id="date"
                            type="text"
                            class="form-control"
                        >
                    </div>
                </div>

                <div class="col">
                    <div class="form-group mb-2">
                        <div class="form-check">
                            <input v-model="isDone" class="form-check-input" type="checkbox" value="" id="flexCheckDefault">
                            <label class="form-check-label" for="flexCheckDefault">
                                Completa
                            </label>
                        </div>
                    </div>
                </div>
            </div>

            <div class="mt-2">
                <button
                    type="submit"
                    class="btn btn-primary"
                >
                    ENVIAR
                </button>
            </div>
        </form>
    </div>
</template>

<script>
    export default {
        name: 'UserTodoForm',

        props: {
            todo: {
                type: Object,
                default: () => ({}),
            },

            userId: {
                type: [String, Number],
                default: null,
            },
        },

        data() {
            return {
                id: null,
                title: '',
                description: '',
                dueDate: '',
                isDone: true,
            };
        },

        watch: {
            todo(vl) {
                this.id = vl.id;
                this.title = vl.title;
                this.description = vl.description;
                this.dueDate = vl.due_date;
                this.isDone = vl.is_done;
            },
        },

        methods: {
            submit() {
                const payload = {
                    title: this.title,
                    description: this.description,
                    due_date: this.dueDate,
                    is_done: this.isDone,
                };

                if (this.id) {
                    this.updateTodo(payload);
                } else {
                    this.storeTodo(payload);
                }
            },

            storeTodo(payload) {
                fetch(`http://127.0.0.1:8000/api/users/${this.userId}/todos`,
                    {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                            'Accept': 'application/json',
                        },
                        body: JSON.stringify(payload)
                    })
                    .then(response => response.json())
                    .then((res) => {
                        this.$emit('save', res.data);

                        this.resetForm()
                    });
            },

            updateTodo(payload) {
                fetch(`http://127.0.0.1:8000/api/todos/${this.id}`,
                    {
                        method: 'PUT',
                        headers: {
                            'Content-Type': 'application/json',
                            'Accept': 'application/json',
                        },
                        body: JSON.stringify(payload)
                    })
                    .then(response => response.json())
                    .then((res) => {
                        this.$emit('update', res.data);

                        this.resetForm()
                    });
            },

            resetForm() {
                this.title = '';
                this.description = '';
                this.dueDate = '';
                this.isDone = true;
            },
        },
    };
</script>
