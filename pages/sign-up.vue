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
                        <h5 class="card-title text-center mb-4">Sign Up</h5>
                        <input type="email" class="form-control my-4" id="email" aria-describedby="emailHelp" placeholder="Email" v-model="email">
                        <input type="password" class="form-control my-4" id="password" aria-describedby="emailHelp" placeholder="Contraseña" v-model="password1">
                        <input type="password" class="form-control my-4" id="password" aria-describedby="emailHelp" placeholder="Repetir contraseña"  v-model="password2">
                    </div>
                    <button type="button" class="btn btn-primary btn-lg btn-block" v-on:click="onClick">Registrar</button>
                </div>
               
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return{
            email:'',
            password1:'',
            password2:'',
        }
    },
    methods:{
        async onClick(){
            try{
                if(!this.email || !this.password1 || !this.password2){
                    alert('Campos no completados');
                    return
                }
                const body = JSON.stringify({
                    email: this.email,
                    password1: this.password1,
                    password2: this.password2,
                });
                const res = await fetch('http://localhost:4500/api/user/sign-up',{
                    method:'post',
                    headers:{
                        'Content-Type': 'application/json',
                    },
                    body
                })
                const data = await res.json();
                if(data.error){
                    alert(data.error);
                    return;
                }else{
                    this.$router.push('/sign-in');
                }
            }catch(err){
                console.log(err)
            }
        }
    }
}
</script>