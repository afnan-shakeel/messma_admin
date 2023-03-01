<template>
  <v-container>
    Custom Report 
    <v-col cols="3">
    <v-menu
    :close-on-content-click="false"
    offset-y
    min-width="auto"
  >
  <template v-slot:activator="{ on, attrs }">
    <v-text-field
    v-model="dates"
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
    v-model="dates"
    range
    no-title
    scrollable
  ></v-date-picker>
  </v-menu>
</v-col>
  <v-row v-if="selectedMess && dates">
   <v-col cols="auto">
    <!-- <v-select v-model="selectedMess" :items="messList" item-text="name" item-value="id" @change="getUserMeals()" label="select mess" dense clearable></v-select> -->
    </v-col>
    <v-col cols="auto" v-if="userList">
    <v-select v-model="selectedUser" :items="userList" item-text="name" item-value="user_id" label="select user" dense clearable></v-select>
   </v-col>

   <v-col cols="auto" v-if="(mealList && selectedMess)">
    <v-select v-model="selectedMeal" :items="mealList" item-text="name" item-value="id" label="select meal" dense clearable></v-select>
   </v-col>
   <v-btn v-if="dates && (selectedUser || selectedMeal)" @click="submitReport()">submit</v-btn>
   <v-btn v-if="dates && (selectedUser || selectedMeal)" @click="reset()">reset</v-btn>
</v-row>
    <!-- <report-list v-if="reportData" v-bind:reportData="reportData" v-bind:userList="userList" v-bind:configs="configs"></report-list> -->
    <span><p style='font-family: "Gill Sans", sans-serif;font-size: 1.5em;padding: 18px 12px 2px 32px;' v-if="reportData">{{this.reportData[0] ? this.reportData[0]['_sum']['points'] + ' Points' : '-' }}</p></span>
    <v-container>
    <v-row>
     <report-monthly v-bind:selectedMess="selectedMess"></report-monthly>
    </v-row>
  </v-container>
</v-container>
</template>

<script>
export default {
    name:'daily-report',
    props:['selectedMess'],
    data(){
        return{
            meals: null,
            days: null,
            selectedDay: null,
            dates: null,
            messList: null,
            userList: null,
            mealList: null,
            userlList: null,
            configs: null,
            selectedMeal:null,
            selectedUser:null,
            reportData: null,

        }
    },
    mounted(){
        this.getUserMeals();
    },
    methods:{
        async downloadReport(){
            const res = await this.$axios.get('report/download?mess_id=2&month=12&year=2022',{
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
        async getUserMeals(){
            const mealsRes = await this.$axios.get('/ext/meals')
            const messConfigRes = await this.$axios.get(`/mess/${this.selectedMess}/config`,{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            if(messConfigRes.data && messConfigRes.data.message !== 'Success') return
            const config_list = messConfigRes.data.data.meal_config.split(',')
            const configMeals = mealsRes.data.data.filter(x=>config_list.includes(x.name))
            this.mealList = configMeals

            const userRes = await this.$axios.get(`/inmate/${this.selectedMess}`,{
                    headers:{
                        Authorization:this.$storage.getUniversal('token')
                    }
                });
            if(userRes.data && userRes.data.message !== 'Success') return
            this.userList = userRes.data.data
        },
        async getConfigs(){
            const res = await this.$axios.get(`/menu-config/${this.selectedMess}`,{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            if(res.data.message !== 'Success'){
                this.$toasted.error(`failed to fetch configs`)
                return
            }
            return res.data.data
        },
        async submitReport(){
            if( this.dates.length >= 2 && this.selectedMeal){
                this.$toasted.info(`Date range not allowed for meal reports `)
                return;
            }
            const payload = {}
            payload.mess_id = this.selectedMess
            if(this.selectedUser){
                payload.user_id = this.selectedUser
            }
            this.configs = await this.getConfigs() 
            var currentDay = new Date(this.dates[0]).getDay()
            if (currentDay === 0) currentDay = 7
            const selectedConfig = this.configs.filter(x=> x.day === currentDay && x.meal_id === this.selectedMeal)
            if(selectedConfig.length > 0){
                payload.config_id = selectedConfig[0].id
            }
            const date1 = this.dates[0]
            const date2 = this.dates[1] ? this.dates[1] : null
            const res = await this.$axios.post(`/hogg/report?date1=${date1}&date2=${date2}`,payload)
            console.log('report res ',res.data)
            if(res.data && res.data.message !== 'Success') {
                this.$toasted.error(`failed to fetch report`)
                return
            }
            const reportData = res.data.data
            const mealsRes = await this.$axios.get('/ext/meals')
            const dayRes = await this.$axios.get('/ext/days')
            for (let item of reportData){
                if(item.user_id && item.config_id){
                    let userObj = this.userList.filter(x => x.user_id === item.user_id)
                    item.user_name = userObj.length > 0 ? userObj[0].name : null

                    let configObj = this.configs.filter(x=> x.id === item.config_id)
                    if(!(configObj.length > 0)){ 
                        this.$toasted.error(`fail`)
                     }
                    let dayObj = dayRes.data.data.filter(x=>x.id === configObj[0].day)
                    let mealObj = mealsRes.data.data.filter(x=>x.id === configObj[0].meal_id)
                    item.day_name = dayObj.length > 0 ? dayObj[0].name : null
                    item.meal_name = mealObj.length > 0 ? mealObj[0].name : null
                }
                else if(item.user_id){
                    let x = this.userList.filter(x => x.user_id === item.user_id)
                    item.user_name = x.length > 0 ? x[0].name : null 
                }
                else if(item.config_id){
                    let configObj = this.configs.filter(x=> x.id === item.config_id)
                    if(!(configObj.length > 0)){ 
                        this.$toasted.error(`fail`)
                     }
                    let dayObj = dayRes.data.data.filter(x=>x.id === configObj[0].day)
                    let mealObj = mealsRes.data.data.filter(x=>x.id === configObj[0].meal_id)
                    item.day_name = dayObj.length > 0 ? dayObj[0].name : null
                    item.meal_name = mealObj.length > 0 ? mealObj[0].name : null
                }
                else if(item.mess_id){
                    let x = this.messList.filter(x=> x.id === item.mess_id)
                    item.mess_name = x.length > 0 ? x[0].name : null 
                }
            }
            this.reportData = reportData
            console.log('x',this.reportData)
        },
        reset(){
            this.reportData = null,
            this.selectedUser = null,
            this.selectedMeal = null,
            this.selectedDay = null,
            this.dates = null,
            this.days = null,
            this.selectedConfig = null
        }

    }
}
</script>

<style>

</style>