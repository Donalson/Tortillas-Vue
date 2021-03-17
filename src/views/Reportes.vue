<template>
  <v-container>

    <!-- Encabezado de la pagina -->
    <v-toolbar flat>
      <v-toolbar-title>Reportes</v-toolbar-title>
      <v-divider class="mx-4" inset vertical></v-divider>
      <v-spacer></v-spacer>
      <!-- Campo de filtrado de ventas -->
      <v-text-field
        v-model="Buscar"
        append-icon="mdi-magnify"
        label="Buscar"
        single-line
        hide-details
        color="green"
      ></v-text-field>
      <v-spacer></v-spacer>
    </v-toolbar>

    <v-card
      class="mx-auto text-center"
      color="green"
      dark
      max-width="600"
    >
    <v-card-text>
      <v-sheet color="rgba(0, 0, 0, .12)">
        <v-sparkline
          :value="TTortillas"
          :labels="['Pequeñas','Mediana','Grandes']"
          color="rgba(255, 255, 255, .7)"
          height="100"
          padding="24"
          stroke-linecap="round"
          smooth
        >
          <template v-slot:label="item">
            {{ item.value }}
          </template>
        </v-sparkline>
      </v-sheet>
    </v-card-text>

    <v-card-text>
      <div class="display-1 font-weight-thin">
        Pedidos de Tortillas
      </div>
    </v-card-text>

    <v-divider></v-divider>

    <!-- <v-card-actions class="justify-center">
      <v-btn
        block
        text
      >
        Go to Report
      </v-btn>
    </v-card-actions> -->
  </v-card>

  </v-container>
</template>

<script>
import axios from "axios";
import { mapMutations } from 'vuex';
import alertify from "vue-alertify";
import moment from 'moment';
export default {
  data() {
    return{
      //Arrays de informacion
      Clientes: [],Tortillas: [],Pedidos:[], Ventas: [],
      //Variable
      Buscar:'',TTortillas:[]
    };
  },

  created() {
    this.MostrarLoading({Titulo:'Accediendo a la información', Color:'green'})
    this.GetClientes();this.GetTortillas();this.GetPedidos();this.GetVentas()
    this.OcultarLoading() 
  },

  computed: {
  },

  methods: {
    ...mapMutations(['MostrarLoading','OcultarLoading']),

    async GetClientes(){
      let Clientes
      try {
        await axios.get(`http://192.168.1.4:3000/Clientes`).then((res)=>{
          Clientes = res.data
        })
      } catch (error) {
        console.log(error)
      }
      this.Clientes = await Clientes
    },

    async GetTortillas(){
      let Tortillas
      try {
        await axios.get(`http://192.168.1.4:3000/Tortillas`).then((res)=>{
          Tortillas = res.data
        })
      } catch (error) {
        console.log(error)
      }
      this.Tortillas = await Tortillas
    },

    async GetPedidos(){
      var Pedidos,Peq=0,Med=0,Gra=0
      try {
        await axios.get(`http://192.168.1.4:3000/Pedidos`).then((res)=>{
          Pedidos = res.data
        })
      } catch (error) {
        console.log(error)
      }
      this.Pedidos = await Pedidos
      for(let i = 0;i < Pedidos.length; i++){
        if(Pedidos[i].Tortilla == 1){
          Gra = Gra + parseInt(Pedidos[i].Cantidad)
        }
        if(Pedidos[i].Tortilla == 2){
          Med = Med + parseInt(Pedidos[i].Cantidad)
        }
        if(Pedidos[i].Tortilla == 3){
          Peq = Peq + parseInt(Pedidos[i].Cantidad)
        }
      }
      this.TTortillas.push(parseInt(Peq),parseInt(Med),parseInt(Gra))
    },

    async GetVentas(){
      let Ventas
      try {
        await axios.get(`http://192.168.1.4:3000/Ventas`).then((res)=>{
          Ventas = res.data
        })
      } catch (error) {
        console.log(error)
      }
      this.Ventas = await Ventas
    },
  },
};
</script>