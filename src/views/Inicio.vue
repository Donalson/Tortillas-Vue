<template>
  <v-container text-aling-center>
    <v-row class="justify-center" wrap>

      <v-col xs="6"
          v-for="(card, index) in cards" :key="index">
          <v-card>
            <v-img
              :src="card.src"
              class="black--text align-end"
              height="20%"
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
        { Desc: 'Pedidos', src: 'https://raw.githubusercontent.com/Donalson/Tortillas-Vue/main/src/assets/Ordenes.png', flex: 6 },
        { Desc: 'Clientes', src: 'https://raw.githubusercontent.com/Donalson/Tortillas-Vue/main/src/assets/Clientes.png', flex: 6 },
        { Desc: 'Tortillas', src: 'https://raw.githubusercontent.com/Donalson/Tortillas-Vue/main/src/assets/Tortilla.png', flex: 6 },
        { Desc: 'Ventas', src: 'https://raw.githubusercontent.com/Donalson/Tortillas-Vue/main/src/assets/Ventas.png', flex: 6 },
      ],
    }),
    
  components: {
  }
}
</script>
