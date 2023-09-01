<template>
  <v-container>
    <v-select v-if="messList" v-model="selectedMess" v-on:change="getUser()" :items="messList" item-text="name" item-value="id" label="select mess" dense clearable></v-select>
    <v-select v-if="selectedMess" v-model="selectedUser" v-on:change="getReport()" :items="userList" item-text="name" item-value="user_id" label="select user" dense clearable></v-select>
    <v-simple-table v-if="selectedUser && data">
        <thead>
            <tr>
                <th>Dish Name</th>
                <th>Points</th>
                <th>Status</th>
                <th>Next Status</th>
                <th>Fine</th>
                <th>created at</th>
                <!-- <th>created by</th> -->
                <th>updated at</th>
                <!-- <th>updated by</th> -->
            </tr>
        </thead>
        <tbody>
            <tr v-for="item in data" :key="item.id">
                <td>{{ item.menu_config.dish_name }}</td>
                <td>{{ item.points }}</td>
                <td>{{ item.status }}</td>
                <td>{{ item.next }}</td>
                <td>{{ item.fine }}</td>
                <td>{{ new Date(item.created_at).toLocaleString() }}</td>
                <!-- <td>{{ item.created_by }}</td> -->
                <td>{{ new Date(item.updated_at).toLocaleString() }}</td>
                <!-- <td>{{ item.updated_by }}</td> -->
            </tr>
        </tbody>

    </v-simple-table>
  </v-container>
</template>

<script>
export default {
    name:'detail-report',
    data(){
        return{
        data: null,
        userList: null,
        messList: null,
        selectedMess: null,
        selectedUser: null,
        }
    },
    mounted(){
        this.getMess();
        // this.getUser();
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
        async getUser(){
            const userRes = await this.$axios.get(`/inmate/${this.selectedMess}`,{
                    headers:{
                        Authorization:this.$storage.getUniversal('token')
                    }
                });
                
            if(userRes.data && userRes.data.message !== 'Success') return
            this.userList = userRes.data.data

        },
        async getReport(){
            var user_id = 11
            const res = await this.$axios.post(`/report/detail-log?user_id=${user_id}`)
            console.log('detail-log res', res.data)
            if(res.data && res.data.message!=='Success') {
                this.$toasted.error(`failed to fetch details`)
                return;
            }
            this.data = res.data.data
        }
    }

}
</script>

<style>

</style>