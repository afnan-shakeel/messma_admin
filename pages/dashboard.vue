<template>
  <v-container>
    <!-- <h2>dashboard</h2> -->
    <v-card v-if="dashboard.length > 0">
        <!-- {{ dashboard }} -->
        <v-row v-for="item in dashboard" :key="item">
            <v-card-text><h2>{{Object.keys(item)[0]}}</h2><p>{{ Object.values(item)[0] }}</p></v-card-text>
        </v-row>
    </v-card>
</v-container>
</template>

<script>
export default {
    name:'Dashboard',
    data(){
        return{
            configs: null,
            selectedMess:2,
            dashboard:[],
            selectedMeal:[1,2,3,4]
        }
    },
    mounted(){
        this.getConfigs()
    },
    methods:{
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
            this.configs = res.data.data
            console.log('configs', this.configs)

            var currentDay = new Date().getDay()
            if (currentDay === 0) currentDay = 7
            const selectedConfig = this.configs.filter(x=> x.day === currentDay)
            console.log("selectedConfig",selectedConfig)
            this.configs = selectedConfig
            this.report()

        },
        async report(){
            var configs = []
            for (let item of this.configs){
                configs.push(item.id)
            }
            var payload = {
                mess_id:2,
                configs: configs
            }
            console.log('payload',payload)
            var date1 = new Date().toISOString().split('T')[0]
            console.log(date1)
            const res = await this.$axios.post(`/report/today-count`)
            console.log('dashboard res',res)
            if(res.data && res.data.message === 'Fail'){
                this.$toasted.error(`failed`)
                return
            } 
            console.log('data',res.data.data)
            var mealReport = res.data.data
            for (let item of mealReport){
                let meal_id = this.configs.filter(x=>x.id === item.config_id)[0].meal_id
                if(meal_id === 1) this.dashboard.push({'Breakfast' : item['_sum']['points']})
                if(meal_id === 2) this.dashboard.push({'Lunch' : item['_sum']['points']})
                if(meal_id === 3) this.dashboard.push({"Tea" : item['_sum']['points']})
                if(meal_id === 4) this.dashboard.push({'Dinner' : item['_sum']['points']})
            }
            console.log('x',this.dashboard)
        }
    }

}
</script>

<style>

</style>