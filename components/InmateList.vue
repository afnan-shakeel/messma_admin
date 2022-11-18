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
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in inmateList" :key="item.id">
                    <td>{{item.id}}</td>
                    <td>{{item.name}}</td>
                    <td>{{item.room}}</td>
                    <td>{{item.mobile}}</td>
                    <td>{{item.email}}</td>
                    <td>{{item.status}}</td>
                </tr>
            </tbody>
        </v-simple-table>
    </v-card>
  </v-container>
</template>

<script>
export default {
    name:'InmateList',
    props:['inmateList'],
    data(){
        return{

        }
    },
    mounted(){

    },
    methods:{
    }

}
</script>

<style>

</style>