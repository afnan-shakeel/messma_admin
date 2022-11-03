<template>
  <v-container>
    <v-select
    :items="items"
    v-model="selected"
    @change="dash()"
    outlined>
    </v-select>
    <v-card v-if="this.selected">{{this.selected}}</v-card>
  </v-container>
</template>

<script>
export default {
    data(){
        return{
            items:["one","two"],
            selected:null
        }
    },
    methods:{
        dash(){
            console.log('selected',this.selected)
        }
    }
}
</script>

<style>

</style>