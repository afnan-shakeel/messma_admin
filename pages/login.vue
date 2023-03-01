<template>
  <v-main>
    <v-container>
      <v-layout align-center justify-center>
      <v-flex xs12 sm12 md4 >
        <v-card v-if="!resetPasscode">
      <v-tabs v-model="tab">
        <v-tab v-for="item in items" :key="item">
            {{item}}
        </v-tab>
      </v-tabs>
      <!-- <v-card v-if="tab==0">
        <v-card-title>Admin login</v-card-title>
        <v-card-text>
        <v-text-field prepend-icon="person" v-model="form.mobile" dense outlined label="Phone Number"></v-text-field>
        <v-text-field prepend-icon="lock" v-model="form.passcode" dense outlined label="Passcode" type="password"></v-text-field>
        <v-btn color="primary" @click="login()">Submit</v-btn>
        </v-card-text>
      </v-card>
      <v-card v-if="tab==1"> -->
        <!-- <h2>Mess login</h2> -->
        <v-card-text>
        <v-text-field prepend-icon="person" v-model="form.mobile" dense outlined label="Phone Number"></v-text-field>
        <v-text-field prepend-icon="lock" v-model="form.passcode" dense outlined label="Passcode" type="password"></v-text-field>
        </v-card-text>
        <v-card-actions>
        <v-btn color="primary" @click="login()">Submit</v-btn>
        <v-col align="right">
        <a @click="resetPasscode = true"> Forgot passcode?</a>
        </v-col>
        </v-card-actions>
      
      </v-card>
      <v-card v-if="resetPasscode">
        <reset-passcode></reset-passcode>
      </v-card>
      </v-flex>
    </v-layout>
    </v-container>
  </v-main>
</template>

<script>
export default {
    name:'Login',
    layout:'emptyLayout',
    data(){
        return{
            items:["admin login"],
            tab:1,
            form:{
                mobile:null,
                passcode:null
            },
            resetPasscode: false,
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
                  this.setAuth()
                  .then(res=>{
                    console.log('auth res',res)
                    this.$router.push('/dashboard')
                  })
                  .catch(err=>{
                    console.log('auth err',err)
                    return;
                  });

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
          const res = await this.$axios.get('/auth/data',{
            headers: {
              Authorization: this.$storage.getUniversal('token')
            }
          })
          .then(res=>{
            if(res.data.data.scope.includes('admin_access') || res.data.data.scope.includes('mess_access')){
              this.$storage.setUniversal('user',res.data)
              return;
          }else{
            this.$toasted.error('insufficient permission')
            throw Error('permission denied')
          }
          })
          // this.$toasted.error('failed to get auth')
        },
    }
}

</script>