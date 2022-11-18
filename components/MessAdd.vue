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
        <v-text-field v-model="form.name" label="Name" outlined dense clearable></v-text-field>
        <v-text-field v-model="form.address" label="Address" outlined dense clearable></v-text-field>
        <v-text-field v-model="form.mobile" type="number" label="mobile" outlined dense clearable></v-text-field>
        <v-text-field v-model="form.email" label="email" outlined dense clearable></v-text-field>
        <v-btn @click="dash()">SUBMIT</v-btn>
        <v-btn @click="cancel()">CANCEL</v-btn>
      </form>
  </v-row>
    </v-container>
  </template>
  
  <script>
  export default {
      name:'MessAdd',
      data(){
          return{
              form:{},
              selectedId:null,
          }
      },

      mounted(){
      },
      methods:{
          dash(){
            console.log('add form ',this.form)
          },
          cancel(){
            this.form = null;
            this.$emit('refresh');
          }
      }
  }
  </script>
  
  <style>
  
  </style>