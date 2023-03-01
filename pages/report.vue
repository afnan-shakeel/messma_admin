<template>
  <v-container>
    <v-select v-if="messList" v-model="selectedMess" :items="messList" item-text="name" item-value="id" label="select mess" dense clearable></v-select>
    <report-component v-if="selectedMess" v-bind:selectedMess="selectedMess"></report-component>    
</v-container>
</template>

<script>
export default {
    name:'report',
    data(){
        return{
        selectedMess:null,
        messList:null
        }

    },
    mounted(){
        this.getMess()
    },
    methods:{
        async getMess(){
            const messRes = await this.$axios.get('/mess',{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            if(messRes.data && messRes.data.message !== 'Success') return
            this.messList = messRes.data.data
        },

    }
}
</script>

<style>

</style>