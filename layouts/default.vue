<template>
    <v-app>
        <v-navigation-drawer
        floating
        permanent
        app>
        <v-list>
        <v-sub-header>OPTIONS</v-sub-header>

            <v-list-item
             v-for="(item, i) in menuItems" 
             :key="i"
             :to="item.to"
             router
             exact>
                <v-list-item-action>
                    <v-icon>mdi-apps</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                    <v-list-item-title v-text="item.title"></v-list-item-title>
                </v-list-item-content>
            </v-list-item>
        </v-list>

        </v-navigation-drawer>
        <v-main>
            <v-container>
              <Nuxt />
            </v-container>
          </v-main>
    </v-app>
</template>
<script>

export default {
    name:'defaultLayout',
    data() {
        return{
            menuItems:[
                {
                    id: 'dashboard',
                    title: 'Dashboard',
                    to: '/dashboard'
                },
                {
                    id: 'mess',
                    title: 'Mess',
                    to: '/mess'
                },
                {
                    id: 'inmates',
                    title: 'Inmates',
                    to: '/inmates'
                },
            ],
            drawer:true,
        }
        
    },
}
</script>
