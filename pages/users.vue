<template>
    <div class="container">
        <div class="row my-4">
            <a href="#">Cerrar Sesión</a>
        </div>
        <div class="row my-4"></div>
        <div class="row my-4">
            <div class="col-md-6 offset-md-3">
                <h1 class="text-center">Usuarios</h1>
            </div>
        </div>
        <div class="row my-4">
            <div class="col-md-6 offset-md-3 text-center">
                <div v-for="user in users" :key="user._id">
                    <button class="my-4 bg-light" v-on:click="redirect(user._id)">{{ user.email }}</button>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
export default {
    data(){
        return{
            users:[],
        }
    },
    async beforeMount(){
        const token = window.localStorage.getItem('token');
        if(!token){
            this.$router.push('/sign-in');
        }
        await this.getAllUsers(token)
    },
    methods:{
        redirect(id){
            this.$router.push(`/chat/${id}`);
        },
        async getAllUsers(token){
            try{
                const res = await fetch('http://localhost:4500/api/user/listUsers',{
                    headers:{
                        token,
                    }
                });
                const data = await res.json();

                if(data.error){
                    alert(data.error);
                    return this.$router.push('/sign-in');
                }
                const users = data.users;
                for(let i = 0; i < users.length; i++){
                    this.users.push(users[i]);
                }
            }catch(err){
                alert(err)
            }
        }
    }
}
</script>


<style module>
button:hover{
    color:rgba(29, 145, 240, 0.842)
}
</style>