<template>
    <v-app>
        <v-navigation-drawer
        v-model="drawer"
        fixed
        app>
        <!-- <v-sub-header>OPTIONS</v-sub-header> -->

        <v-list>

            <v-list-item
             v-for="(item, i) in menuItems.filter(x=>x.visible === true)" 
             :key="i"
             :to="item.to"
             router
             exact
             color="primary"
             >     
                <v-list-item-action>
                    <v-icon>mdi-apps</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                    <v-list-item-title v-text="item.title"></v-list-item-title>
                </v-list-item-content>
            </v-list-item>
        </v-list>
        </v-navigation-drawer>
        <v-app-bar
        fixed
        dense
        app
        >

        <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
        <v-spacer></v-spacer>
        <v-menu offset-y>
            <template v-slot:activator="{ on, attrs }">
                <span> {{ 'name' }} </span>
              <v-btn icon v-bind="attrs" v-on="on">
                <v-icon>mdi-account</v-icon>
              </v-btn>
            </template>
            <v-list>
              <v-list-item to='/profile'>Profile</v-list-item>
              <v-list-item @click="logout()">Logout</v-list-item>
            </v-list>
          </v-menu>
              </v-app-bar>
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
            menuItems:[],
            drawer:false,
            showDotlist: false,
        }
        
    },
    mounted(){
        const user = this.$storage.getUniversal('user')
        const scope = user.data.scope
        this.menuItems = [
            {
                id: 'dashboard',
                title: 'Dashboard',
                to: '/dashboard',
                visible: scope.indexOf('admin_access') > -1 ||  scope.indexOf('mess_access') > -1

            },
            {
                id: 'user',
                title: 'User',
                to: '/user',
                visible: scope.indexOf('admin_access') > -1

            },
            {
                id: 'mess',
                title: 'Mess',
                to: '/mess',
                visible: scope.indexOf('admin_access') > -1 ||  scope.indexOf('mess_access') > -1
            },
            {
                id: 'roles',
                title:'Assign roles',
                to: '/role',
                visible: scope.indexOf('admin_access') > -1 ||  scope.indexOf('mess_access') > -1
            },
            {
                id: 'inmates',
                title: 'Inmates',
                to: '/inmates',
                visible: scope.indexOf('admin_access') > -1 ||  scope.indexOf('mess_access') > -1
            },
            {
                id: 'menu-config',
                title: 'Menu config',
                to: '/menu-config',
                visible: scope.indexOf('admin_access') > -1 ||  scope.indexOf('mess_access') > -1
            },
            {
                id: 'report',
                title: 'Report',
                to: '/report',
                visible: scope.indexOf('admin_access') > -1 ||  scope.indexOf('mess_access') > -1
            },
            {
                id: 'detail-review',
                title: 'Detail Review',
                to: '/detail-review',
                visible: scope.indexOf('admin_access') > -1 ||  scope.indexOf('mess_access') > -1
            },
        ]
    },
    methods:{
        logout(){
            localStorage.clear()
            this.$router.push('/login')
            console.log('logged out')
        }
    }

}
</script>
