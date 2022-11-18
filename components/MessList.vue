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
                </tr>
            </thead>
            <tbody>
            <tr v-for="item in MessList" :key="item.id">
                <th>{{item.id}}</th>
                <th>{{item.name}}</th>
                <th>{{item.mobile}}</th>
                <th>{{item.address}}</th>
                <th>{{item.email}}</th>
            </tr>
            </tbody>
        </v-simple-table>
    </v-card>
  </v-container>
</template>

<script>
export default {
    name:'MessList',
    props:['MessList'],
    data(){
        return{

        }
    },
    mounted(){
        
    },
    methods:{
        dash(){
            console.log('prop',this.MessList)
        }
    }

}
</script>

<style>

</style>