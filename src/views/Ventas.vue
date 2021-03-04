<template>
  <v-container>

    <v-row>
      <v-col cols="12" sm="6">
        <v-menu
          v-model="VerFechaModal"
          :close-on-content-click="false"
          :nudge-right="40"
          transition="scale-transition"
          offset-y
          min-width="auto"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-text-field
              label="Ventas de Fecha"
              v-model="VerFecha"
              prepend-icon="mdi-calendar"
              color="green"
              readonly
              v-bind="attrs"
              v-on="on"
            ></v-text-field>
          </template>
          <v-date-picker
            v-model="VerFecha"
            year-icon="mdi-calendar-blank"
            prev-icon="mdi-skip-previous"
            next-icon="mdi-skip-next"
            color="green"
            @input="FechaFormulario = false"
            @change="GetVentas(VerFecha)"
          ></v-date-picker>
        </v-menu>
      </v-col>
      <v-col cols="12" sm="6">
        <v-select
          color="green"
          :items="Clientes"
          item-text="FullName"
          item-value="IdCliente"
          label="Ventas de Cliente"
          prepend-icon="mdi-account"
          v-model="VerCliente"
          @change="GetVentasCliente(VerCliente)"
        ></v-select>
      </v-col>
    </v-row>

    <v-toolbar flat>
      <v-toolbar-title>Ventas</v-toolbar-title>
      <v-divider class="mx-4" inset vertical></v-divider>
      <v-spacer></v-spacer>
      <v-text-field
        v-model="Buscar"
        append-icon="mdi-magnify"
        label="Buscar"
        single-line
        hide-details
        color="green"
      ></v-text-field>
      <v-spacer></v-spacer>
      <v-dialog v-model="Modal" persistent>
        <template v-slot:activator="{ on, attrs }">
          <v-btn color="green" dark v-bind="attrs" v-on="on">Registrar Ventas</v-btn>
        </template>
        <v-card>
          <v-card-title>
            <span class="headline">Registrar Pedido</span>
          </v-card-title>
          <v-card-text>
            
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="green" text 
              >Guardar <v-icon right>mdi-content-save</v-icon></v-btn
            >
            <v-btn color="red" text @click="Limpiar"
              >Cancelar <v-icon right>mdi-cancel</v-icon></v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-toolbar>

    <v-row>
      <v-col>
        <v-card class="mb-5" v-for="(Venta, index) in Busqueda" :key="index">
          <v-card-title>Antojitos Heidy | Venta No.{{ Venta.IdVenta }} </v-card-title>
          <v-card-subtitle>Cliente: {{ Venta.Nombres }} {{ Venta.Apellidos }} <br> Direccion: {{ Venta.Direccion }} <br> Fecha: {{ FormatoFecha(Venta.Fecha) }}</v-card-subtitle>
          <v-simple-table>
            <thead>
              <tr>
                <th class="text-left">Cantidad</th>
                <th class="text-left">Descripcion</th>
                <th class="text-left">Precio C/U</th>
                <th class="text-left">Subtotal</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(Deta, i) in Ventas[index].Detalles" :key="i">
                <td>{{Deta.Cantidad}}</td>
                <td>{{Deta.Descripcion}}</td>
                <td>Q.{{Deta.Precio}}</td>
                <td>Q.{{Deta.Total}}</td>
              </tr>
              <tr>
                <td class="text-right" colspan="3">Total</td>
                <td>Q.{{Venta.SubTotal}}</td>
              </tr>
              <tr>
                <td class="text-center">Total:Q.{{Venta.Total}}</td>
                <td class="text-center">Pago:Q.{{Venta.Pago}}</td>
                <td class="text-center">Cambio:Q.{{Venta.Cambio}}</td>
              </tr>
            </tbody>
          </v-simple-table>
        </v-card>
      </v-col>
    </v-row>
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
      Ventas: [],Clientes:[],Pedidos:[],
      Modal: false,
      Buscar: "",
      VerFechaModal:false,
      VerFecha: moment(new Date()).subtract(1, 'days').format("YYYY-MM-DD"),
      VerCliente: '',
    };
  },

  created() {
    this.GetVentas(this.VerFecha)
    this.GetClientes()
    this.VerificarPendientes()
  },

  computed: {
    Busqueda() {
      const BuquedaLimpia = this.Buscar.toLowerCase().trim();

      if (!BuquedaLimpia) {
        return this.Ventas;
      } else {
        return this.Ventas.filter(
          (item) =>
            item.Nombres.toLowerCase().includes(BuquedaLimpia) ||
            item.Apellidos.toLowerCase().includes(BuquedaLimpia) ||
            item.Direccion.toLowerCase().includes(BuquedaLimpia)
        );
      }
    },
  },

  methods: {
    ...mapMutations(['MostrarLoading','OcultarLoading']),

    async GetVentas(Fecha) {
      try {
        this.MostrarLoading({Titulo:'Accediendo a la información', Color:'green'})
        var Ventas = []
        await axios.get(`http://192.168.1.4:3000/Ventas/${Fecha}`).then((res) => {
          Ventas = res.data;
        });
        if(Ventas.length > 0){
          this.VerCliente = ''
          for(var i = 0; i < Ventas.length; i++){
            await axios.get(`http://192.168.1.4:3000/DetallesVentas/${Ventas[i].IdVenta}`).then((res) => {
              Ventas[i].Detalles = (res.data)
            });
          }
          this.Ventas = await Ventas
          this.VerFechaModal = false
        }else{
          this.$alertify.error("No hay ventas en la fecha " + Fecha)
          this.Ventas = []
        }
      } catch (e) {
        console.log(e);
      } finally{
        this.OcultarLoading()
      }
    },

    async GetVentasCliente(Cliente) {
      try {
        this.MostrarLoading({Titulo:'Accediendo a la información', Color:'green'})
        var Ventas = []
        await axios.get(`http://192.168.1.4:3000/VentasDe/${Cliente}`).then((res) => {
          Ventas = res.data;
        });
        if(Ventas.length > 0){
          this.VerFecha = ''
          for(var i = 0; i < Ventas.length; i++){
            await axios.get(`http://192.168.1.4:3000/DetallesVentas/${Ventas[i].IdVenta}`).then((res) => {
              Ventas[i].Detalles = (res.data)
            });
          }
          this.Ventas = await Ventas
        }else{
          this.$alertify.error("No hay ventas del cliente")
          this.Ventas = []
        }
      } catch (e) {
        console.log(e);
      } finally{
        this.OcultarLoading()
      }
    },

    async GetClientes() {
      try {
        let Get = await axios.get(`http://192.168.1.4:3000/Clientes`)
        for(var i = 0; i < Get.data.length; i++){
          Get.data[i].FullName = Get.data[i].Nombres + " " + Get.data[i].Apellidos
        }
        this.Clientes = await Get.data
      } catch (e) {
        console.log(e);
      }
    },

    async VerificarPendientes(){
      try{
        var Get = await axios.get(`http://192.168.1.4:3000/PedidosARegistrar/`)
        if(Get.data.length > 0){
          var Fechas = ""
          var Fecha = moment(new Date()).format("YYYY-MM-DD")
          for(var i=0; i < Get.data.length; i++){
            if(this.FormatoFecha(Get.data[i].Fecha) != Fecha){
              Fechas += this.FormatoFecha(Get.data[i].Fecha) + ", "
              Fecha = this.FormatoFecha(Get.data[i].Fecha)
            }
          }
          this.$alertify.warning("Pedidos Pendientes de registrar en Ventas de Fecha(s) " + Fechas)
        }else{
          this.$alertify.success("No hay Pedidos pedientes de registrar")
        }
      }catch(e){
        console.log
      }
    },

    async GetPedidos(Fecha) {
      try{
        var Get = await axios.get(`http://192.168.1.4:3000/PedidosARegistrar/${Fecha}`)
        this.Pedidos = await Get.data
      }catch(e){
        console.log
      }
    },

    async Agregar() {
      if (this.Pedido.Cliente > 0 && this.Pedido.Fecha != "") {
        if(this.Pedido.Cantidad != 0 || this.Pedido.Cantidad2 != 0 || this.Pedido.Cantidad3 != 0){

          var infoinicial = {Fecha: this.Pedido.Fecha, Cliente: this.Pedido.Cliente};

          if(this.Pedido.Cantidad > 0){
            var params = infoinicial;
            params.Tortilla = 1
            params.Cantidad = this.Pedido.Cantidad
            try{
              await axios.post(`http://192.168.1.4:3000/Pedidos`, params)
            } catch(e){
              console.log(e)
            }
          }

          if(this.Pedido.Cantidad2 > 0){
            var params = infoinicial;
            params.Tortilla = 2
            params.Cantidad = this.Pedido.Cantidad2
            try{
              await axios.post(`http://192.168.1.4:3000/Pedidos`, params)
            } catch(e){
              console.log(e)
            }
          }

          if(this.Pedido.Cantidad3 > 0){
            var params = infoinicial;
            params.Tortilla = 3
            params.Cantidad = this.Pedido.Cantidad3
            try{
              await axios.post(`http://192.168.1.4:3000/Pedidos`, params)
            } catch(e){
              console.log(e)
            }
          }

          this.GetPedidos(this.Pedido.Fecha);
          this.Modal = false;
          this.Limpiar();
          this.$alertify.success("Pedido Registrado");
        }else{
          this.$alertify.error("Debe llenar al menos una cantidad");
        }
      } else {
        this.$alertify.error("Complete el formulario primero");
      }
    },

    ConfirmarE(item) {
      const detalles ={Cliente: item.Cliente, Fecha: moment(item.Fecha).format("YYYY-MM-DD")}
      this.$alertify.confirmWithTitle(
        "Eliminar Pedido","Desea eliminar el pedido de " + item.Nombres + " " + item.Apellidos,
        () => this.Eliminar(detalles)
        ,
        () => this.$alertify.error("Eliminacion Cancelada")
      );
    },

    Limpiar() {
      this.Modal = false;
    },

    FormatoFecha(Fecha){
      let Formateada = moment(Fecha).format("YYYY-MM-DD")
      return Formateada
    },

  },
};
</script>