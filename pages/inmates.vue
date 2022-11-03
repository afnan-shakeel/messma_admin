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
            selected:null
        }
    },
    methods:{
        dash(){
            console.log('selected ',this.selected)
        }
    }
}
</script>

<style>

</style>