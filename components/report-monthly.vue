<template>
  <v-container>
<v-row>
Generate Monthly Sheet
</v-row>

<v-row>
    <v-col cols="3">
    <v-menu
    :close-on-content-click="true"
    offset-y
    min-width="auto"
  >
  <template v-slot:activator="{ on, attrs }">
    <v-text-field
    v-model="date"
      label="select date"
      prepend-icon="mdi-clock-time-four-outline"
      readonly
      v-bind="attrs"
      v-on="on"
      outlined
      dense
      clearable
    ></v-text-field>
  </template>
  <v-date-picker
    v-model="date"
    type="month"
    @change="report()"    
    scrollable
  ></v-date-picker>
  </v-menu>
  <!-- </v-row> -->
  
</v-col>
<v-col>
  <v-btn v-if="date && result" @click="downloadReport()">Export</v-btn>
</v-col>
</v-row>
<v-row v-if="date">
    <!-- <p v-if="result">{{ result }}</p> -->
    <v-simple-table v-if="result">
        <thead>
            <tr>
                <th>Name</th>
                <th>Room</th>
                <th>Points</th>
                <th>Fine (Rs)</th>
                <th>Mobile</th>
                <th>action</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="item in result" :key="item.user_id">
                
                <td>{{item.name}}</td>
                <td>{{item.room}}</td>
                <td>{{item.total_points}}</td>
                <td>{{item.fine > 0 ? item.fine : '-' }}</td>
                <td>{{item.mobile}}</td>
                <td><v-btn color="error" v-if="item.fine>0" x-small @click="removeFine(item.user_id)">remove fine</v-btn></td>
            </tr>
        </tbody>
    </v-simple-table>
    {{ selectedMess }}
</v-row>
  </v-container>
</template>

<script>

export default {
    name:'report-monthly',
    props:['selectedMess'],
    data(){
        return{
            date:null,
            result: null
        }
    },
    methods:{
        async report(){
            console.log('report function')
            var month = this.date.split('-')[1]
            var year = this.date.split('-')[0]
            const res = await this.$axios.post(`report/monthly?mess_id=${this.selectedMess}&month=${month}&year=${year}`)
            console.log('monthly res', res)
            this.result = res.data.data
        },

        async downloadReport(){
            var month = this.date.split('-')[1]
            var year = this.date.split('-')[0]
            const res = await this.$axios.get(`report/download?mess_id=${this.selectedMess}&month=${month}&year=${year}`,{
                responseType: 'blob',
        })
            console.log('download res', res)
            
            const blob = new Blob([res.data], { type: res.data.type });    
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = 'report';
            link.click()
            URL.revokeObjectURL(link.href)
            
        },
        async removeFine(user_id){
          var month = this.date.split('-')[1]
          var year = this.date.split('-')[0]
          console.log('token x',this.$storage.getUniversal('token'))
          const res = await this.$axios.post(`/report/remove-fine?user_id=${user_id}&month=${month}&year=${year}`,{},{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
          });
          console.log('remove-fine res',res)
          if(res.data.message && res.data.message!=='Success'){
            this.$toasted.error(`action failed - ${res.data.content}`)
            return;
          }
          this.$toasted.info(`fine removed`)
          this.report();  
        }

    }
}
</script>

<style>

</style>