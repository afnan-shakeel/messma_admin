<template>
  <v-container>
    <h2>dashboard</h2>
    <v-btn @click="dash()">click</v-btn>
</v-container>
</template>

<script>
export default {
    name:'Dashboard',
    
    methods:{
        async dash(){
            const res = await this.$axios.get('/api/mess')
            console.log('res ', res)
        }
    }

}
</script>

<style>

</style>