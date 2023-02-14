<template>
    <v-container>
        <v-select
        :items="MessList"
        item-text="name"
        item-value="id"
        v-model="selectedId"
        @change="getConfigs()"
        label="Select mess"
        outlined
        clearable>
        </v-select>
        <v-btn v-if="selectedId" small @click="rightDrawer = true" color="primary">ADD CONFIG</v-btn>
        <!-- <v-btn small @click="editDrawer = true" color="primary">EDIT CONFIG</v-btn> -->
        <v-dialog
            v-model="editDialog"
            width="500"
            v-if="selectedId"
            >
            <template v-slot:activator="{ on, attrs }">
                <v-btn
                color="red lighten-2"
                dark
                small
                v-bind="attrs"
                v-on="on"
                >
                EDIT CONFIG
                </v-btn>
            </template>

            <v-card>
                <v-card-title class="text-h5 grey lighten-2">
                Edit Config
                </v-card-title>

                <v-card-actions>
                <v-col cols="auto"           
                sm="10"
                md="4"
                lg="4">
                <v-select :items="days" item-text="name" item-value="id" v-model="selectedDay" label="select day" dense outlined clearable></v-select>
                </v-col>
                <v-col cols="auto"
                sm="10"
                md="4"
                lg="4">
                <v-select :items="meals" item-text="name" item-value="id" v-model="selectedMeal" label="select meal" dense outlined clearable></v-select>
                </v-col>
                <v-spacer></v-spacer>
                <v-btn
                    color="primary"
                    
                    @click="editProceed()"
                >
                    Proceed
                </v-btn>
                </v-card-actions>
            </v-card>
            </v-dialog>
        <menu-config-table v-if="(this.selectedId && this.configList)" v-bind:selectedId="selectedId" v-bind:configList="configList"></menu-config-table>
        <v-navigation-drawer
        absolute
        floating
        right
        permanent
        v-if="this.rightDrawer && selectedId ">
        <menu-config-add 
            v-bind:selectedId="selectedId"
            v-bind:selectedConfig="selectedConfig"
            v-bind:meals="meals" 
            v-bind:days="days" 
            v-on:refresh="refresh"></menu-config-add>
        </v-navigation-drawer>
    </v-container>
</template>

<script>

export default {
    name:'menu-config',
    data(){
        return{
            MessList : [],
            selectedId:null,
            days:null,
            meals:null,
            rightDrawer: false,
            configList:null,
            editDialog: false,
            selectedDay: null,
            selectedMeal: null,
            selectedConfig: null,
        }
    },
    mounted(){
        this.getMess();
        this.getDayMeals();
    },
    methods:{
        async getDayMeals(){
            const dayRes = await this.$axios.get('/ext/days')
            const mealsRes = await this.$axios.get('/ext/meals')
            this.days = dayRes.data.data
            this.meals = mealsRes.data.data
        },
        async getMess(){
            const res = await this.$axios.get('/mess', {
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            })
            const MessList = res.data.data
            this.MessList = MessList
        },
        async getConfigs(){
            this.configList = null
            if(!this.selectedId) return
            const res = await this.$axios.get(`/menu-config/${this.selectedId}`,{
                headers:{
                    Authorization: this.$storage.getUniversal('token')
                }
            });
            console.log('get configs',res.data)
            if(res.data.message !== 'Success') this.$toasted.error(`failed to fetch configs`)
            this.configList = JSON.stringify(res.data.data)
            console.log('configList',this.configList)

        },
        editProceed(){
            console.log(this.selectedMeal,this.selectedDay)
            this.selectedConfig = JSON.parse(this.configList).filter( (x)=> x.day == this.selectedDay && x.meal_id == this.selectedMeal)[0].id
            console.log('x',this.selectedConfig)
            if(!this.selectedConfig){
                this.$toasted.error(`config not added`)
                return
            }
            this.editDialog = false
            this.rightDrawer = true
        },
        refresh(){
            this.getConfigs();
            this.rightDrawer = false
            this.selectedDay = null
            this.selectedMeal = null
            this.selectedConfig = null
        }  
    }
}
</script>

<style>

</style>