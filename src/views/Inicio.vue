<template>
  <v-container text-aling-center>
    <v-row class="justify-center" wrap>

      <v-col
          v-for="(card, index) in cards" :key="index">
          <v-card>
            <v-img
              :src="card.src"
              class="black--text align-end"
              gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
              height="200px"
            >
              <v-card-title v-text="card.Desc"></v-card-title>
            </v-img>

          </v-card>
        </v-col>
    </v-row>
  </v-container>
</template>

<script>

export default {
  name: 'Inicio',

  data: () => ({
      cards: [
        { Desc: 'Pedidos', src: '../assets/Ordenes.png', flex: 6 },
        { Desc: 'Clientes', src: '../assets/Clientes.png', flex: 6 },
        { Desc: 'Tortillas', src: '../assets/Tortilla.png', flex: 6 },
        { Desc: 'Ventas', src: '../assets/Ventas.png', flex: 6 },
      ],
    }),
    
  components: {
  }
}
</script>
