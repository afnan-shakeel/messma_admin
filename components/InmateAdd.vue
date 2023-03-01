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
        <v-text-field v-model="form.inmate_id" label="inmate id" outlined dense clearable :disabled="selectedInm ? true:false"></v-text-field>
        <v-text-field v-model="form.name" label="name" outlined dense clearable></v-text-field>
        <v-text-field v-model.number="form.mobile" type="number" label="mobile" outlined dense clearable :disabled="selectedInm ? true:false"></v-text-field>
        <v-text-field v-model="form.email" label="email" outlined dense clearable :disabled="selectedInm ? true:false"></v-text-field>
        <v-text-field v-model="form.address" label="address" outlined dense clearable></v-text-field>
        <v-text-field v-model="form.room" label="room" outlined dense clearable></v-text-field>
        <v-btn @click="add()">SUBMIT</v-btn>
        <v-btn @click="cancel()">CANCEL</v-btn>
    </form>
    </v-row>
  </v-container>
</template>

<script>
export default {
    name:'InmateAdd',
    props:['selectedId','selectedInm'],
    data(){
        return{
            form:{}
        }
    },
    mounted(){
        this.fetchInmate();
    },
    methods:{
        async add(){
            const payload = this.form
            if(payload.user_id){
                console.log('updating',payload.user_id)
            }
            payload.status = 'ACTIVE'
            console.log('submitted',this.form,this.selectedId,this.selectedInm)

            await this.$axios.post(`/inmate/${this.selectedId}`,payload,{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            }).then(res=>{
                if(res.data.message==='Success'){
                    console.log('inserted data',res.data.data)
                    if(payload.user_id){
                        this.$toasted.success('updated')
                    }else{
                        this.$toasted.success('REGISTERED')
                    }
                    this.$emit('refresh')
                }else{
                    this.$toasted.error('failed')
                    console.log('fail to add inmate',res)
                }
            }).catch(err=>{
                this.$toasted.error('failed')
                console.log('catch err',err)
                return;
            })
        },
        cancel(){
            this.form = null
            this.$emit('refresh')
        },
        async fetchInmate(){
            if(!this.selectedInm) return
            const res = await this.$axios.get(`/inmate/`+this.selectedId+`/`+this.selectedInm,{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            console.log('xx',res.data)
            const data = res.data.data[0]
            this.form = {
                user_id: data ? data.user_id : null,
                inmate_id: data ? data.inmate_id : null,
                name: data ? data.name : null,
                mobile: data ? data.mobile : null,
                address: data ? data.address : null,
                room: data? data.room: null
            }
        }
    }
}
</script>

<style>

</style>