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
      let Pedidos
      try {
        await axios.get(`http://192.168.1.4:3000/Pedidos`).then((res)=>{
          Pedidos = res.data
        })
      } catch (error) {
        console.log(error)
      }
      this.Pedidos = await Pedidos
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