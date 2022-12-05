<template>
    <v-container>
      <v-row justify="center">
        <v-col
          cols="auto"
          sm="10"
          md="8"
          lg="6"
        ></v-col>
      <form>
        <strong>Profile</strong>
        <v-text-field v-model="form.name" label="Name" outlined dense clearable :disabled="selectedId ? true:false"></v-text-field>
        <v-text-field v-model="form.address" label="Address" outlined dense clearable></v-text-field>
        <v-text-field v-model="form.city" label="City" outlined dense clearable></v-text-field>
        <v-text-field v-model.number="form.pincode" label="Pincode" outlined dense clearable></v-text-field>
        <v-text-field v-model.number="form.mobile" label="mobile" outlined dense clearable :disabled="selectedId ? true:false"></v-text-field>
        <v-text-field v-model="form.email" label="email" outlined dense clearable :disabled="selectedId ? true:false"></v-text-field>
        <v-btn @click="add()">SUBMIT</v-btn>
        <v-btn @click="cancel()">CANCEL</v-btn>
      </form>
  </v-row>
    </v-container>
  </template>
  
  <script>
  export default {
      name:'MessAdd',
      props:['selectedId'],
      data(){
          return{
              form:{},
          }
      },

      mounted(){
        this.fetchMess();
      },
      methods:{
          async add(){
            const payload = this.form
            payload.status = 'ACTIVE'
            if (payload.id){
              console.log('updating',payload.id)
            }
            console.log('add payload ',payload)
            const result = await this.$axios.post('/mess',payload,{
              headers:{
                Authorization: this.$storage.getUniversal('token')
              }
            })
            if(result.data.message === 'Success'){
              if(result.data.message === 'Success' && payload.id){
                this.$toasted.success('mess updated')
              }else{
                this.$toasted.success('mess registered')
              }
              this.form = null;
              this.$emit('refresh');
            }else{
              this.$toasted.error('failed')
            }
          },
          cancel(){
            this.form = null;
            this.$emit('refresh');
          },
          async fetchMess(){
            if(this.selectedId === null) return
            const res = await this.$axios.get(`/mess/`+this.selectedId)
            if(res.data && res.data.message !== 'Success') return
            this.form = {
              id: res.data && res.data.data ? res.data.data.id : null,
              name: res.data && res.data.data ? res.data.data.name:'',
              address: res.data && res.data.data ? res.data.data.address:'',
              city: res.data && res.data.data ? res.data.data.city:'',
              pincode: res.data && res.data.data ? res.data.data.pincode:'',
              mobile: res.data && res.data.data ? res.data.data.mobile:'',
              email: res.data && res.data.data ? res.data.data.email:''
            }
          }
      }
  }
  </script>
  
  <style>
  
  </style>