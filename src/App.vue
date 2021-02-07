<template>
  <v-app>

    <v-app-bar app color="success" dark dense>

      <v-app-bar-nav-icon @click="mver = !mver" class="d-md-none"></v-app-bar-nav-icon>

      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />
      </div>

      <v-toolbar-title>Antojitos Heidy</v-toolbar-title>
      <v-spacer></v-spacer>
      <div class="d-none d-md-block">
        <v-btn plain> Pedidos <v-icon right>mdi-view-list-outline</v-icon></v-btn>
        <v-btn plain> Clientes <v-icon right>mdi-account</v-icon></v-btn>
        <v-btn plain> Tortillas <v-icon right>mdi-alpha-t-circle</v-icon></v-btn>
        <v-btn plain> Ventas <v-icon right>mdi-receipt</v-icon></v-btn>
      </div>
      <v-btn icon>
        <v-icon>mdi-magnify</v-icon>
      </v-btn>

    </v-app-bar>

    <v-navigation-drawer app v-model="mver" temporary color="success">
      <v-row wrap>
        <v-col cols="4"></v-col>
        <v-col class="justify-center" cols="3">
          <v-img src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png" height="45" width="40"/>
        </v-col>
        <v-col cols="4"></v-col>
        <v-col cols="12" class="text-center">
          <p class="white--text headline">Antojitos Heidy</p>
        </v-col>
        <v-col cols="12">

          <v-list>
            
            <v-list-item-group class="white--text">

              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-view-list-outline</v-icon>
                  <v-list-item-content>
                    <v-list-item-title>Pedidos</v-list-item-title>
                  </v-list-item-content>
                </v-list-item-icon>
              </v-list-item>

              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-account</v-icon>
                  <v-list-item-content>
                    <v-list-item-title>Clientes</v-list-item-title>
                  </v-list-item-content>
                </v-list-item-icon>
              </v-list-item>

              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-alpha-t-circle</v-icon>
                  <v-list-item-content>
                    <v-list-item-title>Tortillas</v-list-item-title>
                  </v-list-item-content>
                </v-list-item-icon>
              </v-list-item>

              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-receipt</v-icon>
                  <v-list-item-content>
                    <v-list-item-title>Ventas</v-list-item-title>
                  </v-list-item-content>
                </v-list-item-icon>
              </v-list-item>

            </v-list-item-group>

          </v-list>

        </v-col>
      </v-row>
    </v-navigation-drawer>

    <v-main>
      <router-view/>
    </v-main>
  </v-app>
</template>

<script>

export default {
  name: 'App',

  components: {
  },

  data: () => ({
    mver: false,
  }),
};
</script>
