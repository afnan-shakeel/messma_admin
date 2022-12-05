<template>
    <v-container>
        <v-select
        :items="MessList"
        item-text="name"
        item-value="id"
        v-model="selectedId"
        @change="getInmates()"
        outlined
        clearable>
        </v-select>
        <v-btn v-if="selectedId" @click="rightDrawer = true">ADD INMATE</v-btn>

        <InmateList v-if="this.inmateList" v-on:refresh="refresh" v-bind:selectedId="selectedId" v-bind:inmateList="inmateList"></InmateList>
        <v-navigation-drawer
        absolute
        floating
        right
        permanent
        v-if="this.rightDrawer">
        <InmateAdd v-on:refresh="refresh" v-bind:selectedInm="null" v-bind:selectedId="selectedId"></InmateAdd>
        </v-navigation-drawer>
    </v-container>
</template>

<script>
import InmateAdd from '~/components/InmateAdd.vue';
import InmateList from '~/components/InmateList.vue'

export default {
  components: { InmateAdd },
    data(){
        return{
            rightDrawer:false,
            selectedId:null,
            items:['one'],
            MessList:null,
            inmateList:null
        }
    },
    mounted(){
        this.getMess();
    },
    methods:{
        async getMess(){
            // this.$toast.error('haha')
            const res = await this.$axios.get('/mess', {
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            })
            const MessList = res.data.data
            this.MessList = MessList
            console.log(MessList)
        },  
        async getInmates(){
            if(this.selectedId){
                const resx = await this.$axios.get(`/inmate/${this.selectedId}`,{
                    headers:{
                        Authorization:this.$storage.getUniversal('token')
                    }
                })
                const inmateList = resx.data.data
                this.inmateList = inmateList
                console.log('inmates',resx.data)
            }
        },
        refresh(){
            this.rightDrawer = false
            this.getInmates();
            console.log('inmates refreshed')
        },
        dash(){
            console.log('selected ',this.selectedId)
        }
    }
}
</script>

<style>

</style>