<template>
    <v-container>
    <v-tabs v-model="tab">
        <v-tab v-for="item in items" :key="item">
            {{item}}
        </v-tab>
        </v-tabs>
      <v-card v-if="tab==0">
        <h2>Admin login</h2>
        <v-text-field v-model="form.mobile" label="Phone Number"></v-text-field>
        <v-text-field v-model="form.passcode" label="Passcode" type="password"></v-text-field>
        <v-btn @click="login()">Submit</v-btn>
      </v-card>
      <v-card v-if="tab==1">
        <h2>Mess login</h2>
        <v-text-field v-model="form.mobile" label="Phone Number"></v-text-field>
        <v-text-field v-model="form.passcode" label="Passcode" type="password"></v-text-field>
        <v-btn @click="login()">Submit</v-btn>
      </v-card>
    </v-container>
  </template>

<script>
export default {
    name:'Login',
    layout:'emptyLayout',
    data(){
        return{
            items:["admin login","mess login"],
            tab:1,
            form:{
                mobile:null,
                passcode:null
            }
        }
    },
    methods:{
        async login(){
            console.log('form ',this.form)
            const payload = this.form
            await this.$axios.post('/auth/login',payload)
              .then(res => {
                if(res.data.access_token){
                  this.$storage.setUniversal('token',res.data.access_token)
                  console.log('access token set')
                  this.setAuth();
                  this.$router.push('/dashboard')
                }else{
                  this.$toasted.error('invalid')
                  console.log('xx',this.$storage.getUniversal('token'))
                }
              })
              .catch( err => {
                console.log('err x ',err)
              })
        },
        async setAuth(){
          console.log('tokk',this.$storage.getUniversal('token'))
          const res = await this.$axios.get('/auth/user',{
            headers: {
              Authorization: this.$storage.getUniversal('token')
            }
          })
          .then(res=>{
            this.$storage.setUniversal('user',res.data)
          })
          this.$toasted.error('failed to get auth')
        }
    }
}

</script>