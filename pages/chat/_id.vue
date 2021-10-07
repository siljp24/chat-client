<template>
    <div class="chat">
        <div class="container-message">
            <div class="row mt-3 ">
                <div class="col-md-6 offset-3">
                    <button type="button" class="btn btn-secondary" v-on:click="comeBack()">Volver</button>
                </div>
            </div>
            <div class="row">
                <div class="col-md-6 offset-md-3 my-4">
                    <div class="message" v-for="message in messages" :key="message._id">
                        <p v-if="message.userOwner == userId" class="text-right my-4 text-primary">{{ message.text }}</p>
                        <p v-else class="text-left my-4 text-success">{{ message.text }}</p>
                    </div>
                    <div class="form-group">
                        <textarea class="form-control" id="textarea" rows="3" v-model="message"></textarea>
                        <div class="text-right mt-4">
                            <button type="button" class="btn btn-info" v-on:click="sendMessage">Enviar</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return{
            messages:[],
            userId: undefined,
            paramId: undefined,
            message:'',
        }
    },
    async beforeMount(){
        this.userId = window.localStorage.getItem('userId');
        this.paramId = this.$route.params.id;

        const token = window.localStorage.getItem('token');
        if(!token){
            return this.$router.push('/sign-in');
        }
        await this.getMessages(token);
    },
    methods:{
        comeBack(){
            this.$router.push('/users');
        },
        async sendMessage(){
            try{
                const token = window.localStorage.getItem('token');
                const user1Id = window.localStorage.getItem('userId');
                const user2Id = this.$route.params.id;
                const body = JSON.stringify({
                    user1Id,
                    user2Id,
                    text: this.message,
                    userOwnerId: user1Id,
                })
                const res = await fetch('http://localhost:4500/api/message/create',{
                    method: 'post',
                    headers:{
                        token,
                        'Content-Type': 'application/json',
                    },
                    body
                })

                const data = await res.json();
                if(data.error){
                    alert(data.error);
                }else{
                    this.messages = [];
                    await this.getMessages(token);
                }
                
            }catch(err){
                alert(err);
            }
            
        },
        async getMessages(token){
            try{
                const userOneId = window.localStorage.getItem('userId');
                const userTwoId = this.$route.params.id;

                const body = JSON.stringify({
                    userOneId,
                    userTwoId,
                })
                const res = await fetch('http://localhost:4500/api/message/chat',{
                    method: 'post',
                    headers:{
                        'Content-Type': 'application/json',
                        token,
                    },
                    body
                })
                const data = await res.json();
                if(data.error){
                    alert(data.error);
                    return this.$router.push('/sign-in');
                }
                
                const messages = data.result;
                for(let i = 0; i < messages.length; i++){
                    this.messages.push(messages[i]);
                }
            }catch(err){
                alert(err)
            }
        }
    }
}
</script>