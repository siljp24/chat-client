<template>
    <div class="container">
        <div class="row my-4">
        </div>
        <div class="row my-4">
        </div>
        <div class="row mt-4">
            <div class="col-md-6 offset-md-3">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title text-center mb-4">Sign In</h5>
                        <input type="email" class="form-control my-4" id="email" aria-describedby="emailHelp" placeholder="Email" v-model="email">
                        <input type="password" class="form-control my-4" id="password" aria-describedby="emailHelp" placeholder="Contraseña" v-model="password">
                    </div>
                    <button type="button" class="btn btn-primary btn-lg btn-block" v-on:click="onClick">Enviar</button>
                </div>
               
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return{
            email: '',
            password:'',
        }
    },
    methods:{
        async onClick(){
            try{
                if(!this.email || !this.password){
                    alert('Campos no completados');
                    return
                }
                const body = JSON.stringify({
                    email:this.email,
                    password: this.password,
                })
                const res = await fetch('http://localhost:4500/api/user/sign-in',{
                    method:'post',
                    headers:{
                        'Content-Type': 'application/json',
                    },
                    body   
                });
                const data = await res.json();
                if(data.error){
                    alert(data.error);
                    return
                }
                window.localStorage.setItem('token', data.token);
                window.localStorage.setItem('userId', data.userId);
                this.$router.push('/users');

            }catch(err){
                console.log(err);
            }
        }
    }
}
</script>