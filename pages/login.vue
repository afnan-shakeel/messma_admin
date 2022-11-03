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
        login(){
            console.log('form ',this.form)
            this.$router.push('/dashboard')
        }
    }
}

</script>