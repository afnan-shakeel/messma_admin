<template>
    <div>
        <v-row>
        <v-col><v-select v-model="selectedMess" :items="messList" item-text="name" item-value="id" v-on:change="getUser();getAssignedUsers();" label="mess" dense outlined clearable/></v-col>
        <v-col><v-select v-model="selectedUser" :items="userList" item-text="name" item-value="inmate_id" label="user" dense outlined clearable/></v-col>
        <v-col><v-select v-model="selectedRole" :items="roleList" item-text="name" item-value="id" label="role" dense outlined clearable/></v-col>
        <v-col><v-btn v-on:click="assignRole()">ASSIGN</v-btn></v-col>
        </v-row>
        <v-card>
            <v-simple-table>
                <thead>
                    <tr>
                    <th>ID</th>
                    <th>name</th>
                    <th>mobile</th>
                    <th>role id</th>
                    <th>action</th>
                    </tr>
                </thead>
                <tbody v-if="assignedUser.length>0" >
                    <tr v-for="item in assignedUser" :key="item.user_id">
                        <td>{{item.user_id}}</td>
                        <td>{{item.name}}</td>
                        <td>{{item.mobile}}</td>
                        <td>{{item.role_id}}</td>
                        <td>
                            <v-btn small color="error" v-on:click="deleteRole(item.user_id,item.role_id)">DELETE</v-btn>
                        </td>
                    </tr>
                </tbody>
            </v-simple-table>
        </v-card>
    </div>
</template>

<script>
export default {
    data(){
        return{
            messList:null,
            userList:null,
            roleList:null,
            selectedMess:null,
            selectedUser:null,
            selectedRole:null,
            assignedUser:[]
        }
    },
    mounted(){
        this.getMesses();
        this.getRole();

    },
    methods:{
        async getMesses(){
            const result = await this.$axios.get('/mess',{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            if (result.data.message === 'Success'){
                this.messList = result.data.data
            }else{
                this.$toasted.info(`mess list failed`)
                return;
            }
        },
        async getUser(){
            if(!this.selectedMess){
                this.userList = null
                return;
            }
            const res = await this.$axios.get(`/inmate/${this.selectedMess}`,{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            if (res.data.message === 'Success'){
                this.userList = res.data.data
            }else{
                this.$toasted.error(`user list failed`)
                return;
            }
        },
        async getRole(){
            const res = await this.$axios.get(`/role`,{
                headers:{
                    Authorization: this.$storage.getUniversal(`token`)
                }
            });
            console.log(res.data)
            if(res.data.message === 'Success'){
                this.roleList = res.data.data
            }else{
                this.$toasted.error(`role list failed`)
                return;
            }
        },
        async getAssignedUsers(){
            if(!this.selectedMess){
                this.assignedUser = []
                return;
            } 
            const res = await this.$axios.get(`/role/${this.selectedMess}`,{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            console.log('x',res.data)
            if(res.data.message === 'Success'){
                this.assignedUser = res.data.data
            }
        },
        async assignRole(){
            if (!this.selectedMess || !this.selectedUser || !this.selectedRole){
                this.$toasted.error(`select valid user`)
                return
            }
            const payload = { inmate_id: this.selectedUser, role_id:this.selectedRole }
            const res = await this.$axios.post('/role',payload,{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            if (res.data.message === 'Success'){
                this.$toasted.info('role assigned')
                this.refresh();
            }else{
                this.$toasted.error('fail to assign')
            }
        },
        async deleteRole(user_id,role_id){
            console.log( 'delete ', user_id,role_id)
            const res = await this.$axios.post('/role/delete',{ user_id : user_id},{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            if(res.data.message === 'Success'){
                this.$toasted.success('deleted')
                this.refresh();
            }else{
                this.$toasted.error('failed')
            }
        },
        refresh(){
            this.getUser();
            this.getAssignedUsers();
        }
    }
}
</script>

<style>

</style>