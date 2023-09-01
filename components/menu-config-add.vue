<template>
  <v-container>
    <v-row justify="center">
        <v-col
          cols="auto"
          sm="10"
          md="8"
          lg="6"
        ></v-col>
      <form>
        <strong>Profile</strong>
        <v-select :items="meals" item-text="name" item-value="id" v-model="selectedMeal" label="Meal" :disabled="selectedConfig ? true:false" outlined dense clearable></v-select>
        <v-select :items="days" item-text="name" item-value="id" v-model="selectedDay" label="day" :disabled="selectedConfig ? true:false" outlined dense clearable></v-select>
        <v-text-field v-model="dish_name" label="dish name" outlined dense clearable ></v-text-field>
        <v-text-field v-model="points" label="Points" type="number" outlined dense clearable ></v-text-field>
        <!-- <v-menu
          ref="start_time"
          v-model="start_time"
          :close-on-content-click="false"
          :nudge-right="40"
          :return-value.sync="time1"
          transition="scale-transition"
          offset-y
          max-width="290px"
          min-width="290px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-text-field
              v-model="time1"
              label="start time"
              prepend-icon="mdi-clock-time-four-outline"
              readonly
              v-bind="attrs"
              v-on="on"
            ></v-text-field>
          </template>
          <v-time-picker
            v-if="start_time"
            v-model="time1"
            full-width
            @click:minute="$refs.start_time.save(time1)"
            ampm-in-title
            use-seconds
          ></v-time-picker>
      </v-menu>
        <v-menu
          ref="end_time"
          v-model="end_time"
          :close-on-content-click="false"
          :nudge-right="40"
          :return-value.sync="time2"
          transition="scale-transition"
          offset-y
          max-width="290px"
          min-width="290px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-text-field
              v-model="time2"
              label="end time"
              prepend-icon="mdi-clock-time-four-outline"
              readonly
              v-bind="attrs"
              v-on="on"
            ></v-text-field>
          </template>
          <v-time-picker
            v-if="end_time"
            v-model="time2"
            full-width
            @click:minute="$refs.end_time.save(time2)"
            ampm-in-title
            use-seconds
          ></v-time-picker>
      </v-menu> -->
        <v-btn @click="addConfig()">SUBMIT</v-btn>
        <v-btn @click="cancel()">CANCEL</v-btn>
      </form>
  </v-row>

  </v-container>
</template>

<script>
export default {
    name:'menu-config-add',
    props:['selectedId','selectedConfig'],
    data(){
        return{
            form:null,
            days:null,
            meals:null,
            dish_name:null,
            points:null,
            selectedDay:null,
            selectedMeal:null,
        }
    },
    mounted(){
      this.getDays();
      this.getMeals();
      this.getConfig();

    },
    methods:{
        async getConfig(){
          console.log('xxxxx',this.selectedConfig)
          if(!this.selectedConfig) return
          const res =  await this.$axios.get(`/menu-config/${this.selectedId}/${this.selectedConfig}`,{
            headers:{
              Authorization: this.$storage.getUniversal('token')
            }
          });
          if(res.data.message !== "Success") this.$toasted.error('failed to get config res')
          console.log('selected config ', res.data.data.id)
          if(res.data.data){
            // this.form.id = res.data.data.id
            this.selectedDay = res.data.data.day
            this.selectedMeal = res.data.data.meal_id
            this.dish_name = res.data.data.dish_name
            this.points = res.data.data.points
          }
        },
        async getDays(){
          console.log('day func')
            const res = await this.$axios.get('/ext/days')
            this.days = res.data.data
        },
        async getMeals(){
          const res = await this.$axios.get('/ext/meals')
          this.meals = res.data.data
        },
        async addConfig(){
          const payload = {
            id: this.selectedConfig ? this.selectedConfig : null,
            meal_id : this.selectedMeal,
            day : this.selectedDay,
            points: this.points,
            dish_name: this.dish_name,

          }
          // if( !this.selectedConfig ) payload.id = this.selectedConfig
          const res = await this.$axios.post(`/menu-config/${this.selectedId}`, payload, {
            headers:{
              Authorization:this.$storage.getUniversal('token')
            }
          });
          console.log('add config res', res)
          if(res.data.message === 'Success'){
            if(payload.id){
              this.$toasted.success('config updated')
            }else{
              this.$toasted.success('config added')
            }
            this.refresh();
          }else{
            console.log(res.data.content)
            this.$toasted.error(`failed - ${res.data.content}`)
          }
        },
        cancel(){
          this.refresh();
        },
        refresh(){
          this.form = null
          this.selectedDay = null
          this.selectedMeal = null
          this.$emit('refresh')

        }
    }
}
</script>

<style>

</style>