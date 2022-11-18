<template>
  <v-container>
    <form>
        <v-text-field v-model="form.name" label="name"></v-text-field>
        <v-text-field v-model.number="form.mobile" type="number" label="mobile"></v-text-field>
        <v-text-field v-model="form.email" label="email"></v-text-field>
        <v-text-field v-model="form.address" label="address"></v-text-field>
        <v-text-field v-model="form.room" label="room"></v-text-field>
        <v-btn @click="add()">SUBMIT</v-btn>
        <v-btn @click="cancel()">CANCEL</v-btn>
    </form>
  </v-container>
</template>

<script>
export default {
    name:'InmateAdd',
    props:['selectedId'],
    data(){
        return{
            form:{}
        }
    },
    methods:{
        async add(){
            const payload = this.form
            payload.status = 'ACTIVE'
            console.log('submitted',this.form,this.selectedId)

            await this.$axios.post(`/user/${this.selectedId}`,payload).then(res=>{
                if(res.data.message==='Success'){
                    console.log('inserted data',res.data.data)
                    this.$toast.info('REGISTERED')
                    this.$emit('refresh')
                }else{
                    console.log('fail to add inmate',res)
                }
            })
        },
        cancel(){
            this.form = null
            this.$emit('refresh')
        }
    }
}
</script>

<style>

</style>