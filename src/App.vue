<template>
    <div
        class="container pt-5"
        id="app"
    >
        <div
            v-for="user in users"
            :key="user.id"
            class="card mb-3"
        >
            <div class="card-body">
                {{ user.name }}
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'App',

    data() {
        return {
            users: [],
        };
    },

    mounted() {
        fetch('http://127.0.0.1:8000/api/users')
            .then(response => response.json())
            .then((res) => {
                this.users = res.data;
            });
    },
}
</script>
