<template>
  <v-container>
    <v-card>
        <v-simple-table>
            <thead>
                <tr>
                    <th>
                        ID
                    </th>
                    <th>
                        Name
                    </th>
                    <th>
                        mobile
                    </th>
                    <th>
                        address
                    </th>
                    <th>
                        email
                    </th>
                    <th>
                        Action
                    </th>
                </tr>
            </thead>
            <tbody>
            <tr v-for="item in MessList" :key="item.id">
                <th>{{item.id}}</th>
                <th>{{item.name}}</th>
                <th>{{item.mobile}}</th>
                <th>{{item.address}}</th>
                <th>{{item.email}}</th>
                <th>
                    <v-btn small color="primary" v-on:click="edit(item.id)">edit</v-btn>
                </th>
            </tr>
            </tbody>
        </v-simple-table>
    </v-card>
    <v-navigation-drawer
    absolute
    floating
    right
    permanent
    v-if="editDrawer">
    <MessAdd v-bind:selectedId="mess_id" v-on:refresh="refreshx"></MessAdd>
    </v-navigation-drawer>
  </v-container>
</template>

<script>
export default {
    name:'MessList',
    props:['MessList'],
    data(){
        return{
            editDrawer:false,
            mess_id:null
        }
    },
    mounted(){
        
    },
    methods:{
        dash(){
            console.log('prop',this.MessList)
        },
        async edit(id){
            this.mess_id = id
            this.editDrawer = true
        },
        refreshx(){
            this.editDrawer = false
            this.$emit('refresh')
        }
    }

}
</script>

<style>

</style>