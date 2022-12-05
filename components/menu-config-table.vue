<template>
  <v-container>
    <v-card
    v-if="(this.tableData && this.tableData.length > 0 && this.headers && this.headers.length > 0)"
    >
    <!-- <v-data-table
    :headers="headers"
    :items="tableData"
    class="elevation-1"
  ></v-data-table> -->
  <v-simple-table>
    <thead>
      <tr>
        <th v-for="item in headers" :key="item.value">{{item.text}}</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in tableData" :key="item.meal">
        <td>{{item['meal']}}</td>
        <td>{{item['1']}}</td>
        <td>{{item['2']}}</td>
        <td>{{item['3']}}</td>
        <td>{{item['4']}}</td>
        <td>{{item['5']}}</td>
        <td>{{item['6']}}</td>
        <td>{{item['7']}}</td>
      </tr>
    </tbody>
  </v-simple-table>
  </v-card>
  </v-container>
</template>

<script>
export default {
    props:['selectedId','configList'],
    data(){
        return{
          headersx: [
          {
            text: 'Dessert (100g serving)',
            align: 'start',
            sortable: false,
            value: 'name',
          },
          { text: 'Calories', value: 'calories' },
          { text: 'Fat (g)', value: 'fat' },
          { text: 'Carbs (g)', value: 'carbs' },
          { text: 'Protein (g)', value: 'protein' },
          { text: 'Iron (%)', value: 'iron' },
        ],
        tableDatax: [
          {
            name: 'Frozen Yogurt',
            calories: 159,
            fat: 6.0,
            carbs: 24,
            protein: 4.0,
            iron: '1%',
          },
          {
            name: 'Ice cream sandwich',
            calories: 237,
            fat: 9.0,
            carbs: 37,
            protein: 4.3,
            iron: '1%',
          },
          {
            name: 'Eclair',
            calories: 262,
            fat: 16.0,
            carbs: 23,
            protein: 6.0,
            iron: '7%',
          }
        ],
        headers:[],
        tableData:[],
        }
    },
    mounted(){
      this.getHeaders();
      this.configData();
    },
    methods:{
      async getHeaders(){
        const res = await this.$axios.get('/ext/days')
        if(res.data.message !== 'Success') this.$toasted.error('failed to fetch days')

        const headers = [{
            text: 'MEAL',
            align: 'start',
            sortable: false,
            value: 'meal',
          }]
        
        for (let elem of res.data.data ){
            const header = {}
            header.text = elem.name
            header.value = elem.id,
            header.sortable = false
            headers.push(header) 
        }
        this.headers = headers
        console.log('headers', this.headers)
      },
      async configData(){
        const res = await this.$axios.get(`/mess/${this.selectedId}/config`,{
          headers:{
            Authorization: this.$storage.getUniversal('token')
          }
        });
        if(res.data && res.data.message !== 'Success') return
        const config_res = res.data.data.meal_config.split(',')
        const meal_res = await this.$axios.get('/ext/meals')
        const day_res = await this.$axios.get('/ext/days')
        const days = day_res.data.data
        const configMeals = meal_res.data.data.filter(x=>config_res.includes(x.name))
        // console.log('xx',configMeals, days,JSON.parse(this.configList))
        const tableData = []
        for(var meal of configMeals){
          const data = {
            meal: meal.name
          }
          for ( var day of days){
            data[day.id] = '-'
            for (var config of JSON.parse(this.configList)){
              if(config.meal_id == meal.id && config.day == day.id){
                data[day.id] = config.points 
              }
            }
          }
          
          // console.log(data)
          tableData.push(data)
          // break;
        }
        console.log('tableData',tableData)
        this.tableData = tableData

      }
    }

}
</script>

<style>

</style>