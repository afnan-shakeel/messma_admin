<template>
  <v-container>
    <!-- <v-select
    :items="MessList"
    item-text="name"
    item-value="id"
    v-model="selectedId"
    @change="getMesses()"
    outlined
    dense
    clearable>
    </v-select> -->
    <v-btn v-if="scope && scope.includes('create_mess')" @click="rightDrawer=true">ADD MESS</v-btn>
    <MessList v-if="MessList.length>0" v-bind:MessList="MessList" v-on:refresh="refresh"></MessList>
    <v-navigation-drawer
    absolute
    floating
    right
    permanent
    v-if="this.rightDrawer">
        <MessAdd v-bind:selectedId="null" v-on:refresh="refresh" ></MessAdd>
    </v-navigation-drawer>
 </v-container>
</template>

<script>
import MessList from '~/components/MessList.vue'
import MessAdd from '~/components/MessAdd.vue';

export default {
    data() {
        return {
            items: ["one", "two"],
            MessList: [],
            rightDrawer:false,
            scope : this.$storage.getUniversal('user').data.scope,
            isAllowed:false,
        };
    },
    mounted() {
        this.scope;
        this.getMesses();
    },
    methods: {
        async getMesses() {
            // console.log(this.selectedId)
            const res = await this.$axios.get('/mess',{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            })
            console.log('mess res',res.data)
            this.MessList = res.data.data;
        },
        refresh(){
            this.rightDrawer = false,
            this.getMesses()
            console.log('refershed')
        }
    },
    components: { MessList, MessAdd }
}
</script>


<style>

</style>