<template>
    <div class="container">
        <div class="row my-4">
            <button v-on:click="logout">Cerrar Sesión</button>
        </div>
        <div class="row my-4"></div>
        <div class="row my-4">
            <div class="col-md-6 offset-md-3">
                <h1 class="text-center">Usuarios</h1>
                <h3 v-if="isAdmin" class="text-center">Bienvenido Admin</h3>
            </div>
        </div>
        <div class="row my-4">
            <div class="col-md-6 offset-md-3 text-center">
                <div v-for="user in users" :key="user._id">
                    <button  type="button" class="btn btn-light my-4" v-on:click="redirect(user._id)">{{ user.email }}</button>
                    <button v-if="isAdmin" type="button" class="btn btn-danger" v-on:click="remove(user._id)">X</button>
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
            isAdmin:'',
        }
    },
    async beforeMount(){
        const token = window.localStorage.getItem('token');
        if(!token){
            this.$router.push('/sign-in');
        }
        this.isAdmin = window.localStorage.getItem('admin') === 'true';
        await this.getAllUsers(token)
    },
    methods:{
        redirect(id){
            this.$router.push(`/chat/${id}`);
        },
        async remove(id){
            const token = window.localStorage.getItem('token');
            const body = JSON.stringify({userId: id})
            const res = await fetch('http://localhost:4500/api/user/delete',{
                method:'delete',
                headers:{
                    token,
                    'Content-Type': 'application/json',
                }, 
                body,
            })
            const data = await res.json();
            if(data.error){
                alert(data.error);
                return
            }
            location.reload();
            
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
        },
        logout(){
            this.$router.push('/sign-in')
            window.localStorage.clear();
        }
    }
}
</script>


<style module>
button:hover{
    color:rgba(29, 145, 240, 0.842)
}
</style>