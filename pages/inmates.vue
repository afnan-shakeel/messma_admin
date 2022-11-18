<template>
    <v-container>
        <v-select
        :items="MessList"
        item-text="name"
        item-value="id"
        v-model="selectedId"
        @change="getInmates()"
        outlined>
        </v-select>
        <v-btn @click="rightDrawer = true">ADD INMATE</v-btn>

        <InmateList v-if="this.inmateList" v-bind:inmateList="inmateList"></InmateList>
        <v-navigation-drawer
        absolute
        floating
        right
        permanent
        v-if="this.rightDrawer">
        <InmateAdd v-on:refresh="refresh" v-bind:selectedId="selectedId"></InmateAdd>
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
            const res = await this.$axios.get('/mess')
            const MessList = res.data.data
            this.MessList = MessList
            console.log(MessList)
        },  
        async getInmates(){
            if(this.selectedId){
                const resx = await this.$axios.get(`/user/${this.selectedId}`)
                const inmateList = resx.data.data
                this.inmateList = inmateList
                console.log('inmates',inmateList)
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