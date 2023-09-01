<template>
  <v-container>
    <!-- <h2>dashboard</h2> -->
        <v-simple-table :fixed-header="true" v-if="configs">
            
            <thead>
                <tr>
                    <th width="30px">Meal</th>
                    <th width="6px">Plus</th>
                    <th width="12px">Extra</th>
                    <th width="12px">Total(Count)</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in configs" :key="item.config_id">
                    <td>
                        {{ item.meal }} ({{ item.dish_name }})
                    </td>
                    <td>
                        {{ item.plus }}
                    </td>
                    <td>
                        {{ item.extra }}
                    </td>
                    <td>
                        {{ item.total }}
                    </td>
                </tr>
            </tbody>
        </v-simple-table>
    <!-- </v-card> -->
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
        }
    },
    mounted(){
        this.getDailyCount()
    },
    methods:{
        async getDailyCount(){
            const res = await this.$axios.get(`/report/today-count/${this.selectedMess}`);
            if(res.data.message !== 'Success'){
                this.$toasted.error(`failed to fetch configs`)
                return
            }
            this.configs = res.data.data
            console.log('count res', this.configs)

        }
    }

}
</script>

<style>

</style>