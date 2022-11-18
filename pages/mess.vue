<template>
  <v-container>
    <v-select
    :items="MessList"
    item-text="name"
    item-value="id"
    v-model="selectedId"
    @change="getMesses()"
    outlined
    dense
    clearable>
    </v-select>
    <v-btn @click="rightDrawer=true">ADD MESS</v-btn>
    <MessList v-if="MessList.length>0" v-bind:MessList="MessList"></MessList>
    <v-navigation-drawer
    absolute
    floating
    right
    permanent
    v-if="this.rightDrawer">
        <MessAdd v-if="authData && authData.includes('create_mess')" v-on:refresh="refresh"></MessAdd>
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
            selectedId: null,
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

            const api = this.scope.includes('admin_access') ? '/mess/'+this.selectedId : '/mess'
            const res = await this.$axios.get(api);
            console.log('res mess ? api',res)
            this.MessList = res.data.data;
            console.log("mess ", this.MessList);
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