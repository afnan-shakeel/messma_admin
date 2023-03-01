<template>
  <v-container>
    <!-- <v-card>
    {{ this.reportData[0]['_sum']['points'] }}
    </v-card> -->
    <!-- <v-simple-table v-if="cols">
        <thead>
            <tr>
            <th v-for="item in cols" :key="item">{{item === "_sum" ? 'POINTS' : item}}</th>
            </tr>
            </thead>
            <tbody>
                <tr v-for="item in reportData" :key="item">
                    <td v-for="col in cols" :key="col">{{col === "_sum" ? item[col]["points"] : item[col]}}</td>
                </tr>
            </tbody>
    </v-simple-table> -->
  </v-container>
</template>

<script>
export default {
    name:'report-list',
    props:['reportData'],
    data(){
        return{
            selectedLvl: null,
            cols: null
        }
    },
    mounted(){
        this.lvl()
    },
    methods:{
        lvl(){
            if(this.reportData.length === 0){
                this.$toasted.error(`failed to get report`)
                return
            }
            console.log('reportData',this.reportData)
            const x = Object.keys(this.reportData[0])
            console.log('cols',x)
            const cols = x.filter(x=> !['user_id','mess_id','config_id'].includes(x)) 
            this.cols = cols
        },
        getTexts(){
        }
    }
}
</script>

<style>

</style>