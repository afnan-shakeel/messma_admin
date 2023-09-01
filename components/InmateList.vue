<template>
  <v-container>
    <v-card>
        <v-simple-table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>name</th>
                    <th>room</th>
                    <th>mobile</th>
                    <th>email</th>
                    <th>status</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in inmateList" :key="item.id">
                    <td>{{item.inmate_id}}</td>
                    <td>{{item.name}}</td>
                    <td>{{item.room}}</td>
                    <td>{{item.mobile}}</td>
                    <td>{{item.email}}</td>
                    <!-- <td>{{item.status}}</td> -->
                    <td><v-select v-model="item.status" :items="status" v-on:change="statusChange(item.inmate_id, item.status)"></v-select></td>
                    <td>
                        <v-btn small color="primary" @click="edit(item.user_id)">Edit</v-btn>
                        <v-btn x-small color="error" @click="deleteInmate(item.user_id)">Remove</v-btn>
                    </td>
                </tr>
            </tbody>
        </v-simple-table>
        </v-card>
        <v-navigation-drawer
        absolute
        floating
        right
        permanent
        v-if="this.editDrawer">
        <InmateAdd v-on:refresh="refreshx" v-bind:selectedId="selectedId" v-bind:selectedInm="selectedInm"></InmateAdd>
        </v-navigation-drawer>
  </v-container>
</template>

<script>
export default {
    name:'InmateList',
    props:['inmateList','selectedId'],
    data(){
        return{
            status:['ACTIVE','INACTIVE','CLOSED'],
            editDrawer: false,
            selectedInm:null
        }
    },
    mounted(){

    },
    methods:{
        async statusChange(id,status){
            console.log('status change',id,status)
            const res = await this.$axios.post(`/inmate/status`,{ inmate_id: id, status: status },{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            })
            console.log(res.data)
            if(res.data.message === 'Success'){
                this.$toasted.info(`status updated`)
                this.$emit('refresh')
            }else{
                this.$toasted.error(`failed`)
            }
        },
        edit(id){
            this.editDrawer = true
            this.selectedInm = id
        },
        async deleteInmate(){
            console.log('delete inmate func')
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