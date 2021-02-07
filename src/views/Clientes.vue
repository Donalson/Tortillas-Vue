<template>
  <v-container>
      <v-row wrap class="mt-5">
          <v-col>
            <v-card>
                <v-card-title>
                    Clientes
                <v-spacer></v-spacer>
                <v-text-field
                    v-model="Buscar"
                    append-icon="mdi-magnify"
                    label="Buscar"
                    single-line
                    hide-details
                ></v-text-field>
                </v-card-title>
               <v-data-table dense fixed-header
                    :headers="Encabezados"
                    :items="Clientes"
                    :items-per-page="5"
                    :search="Buscar"
                    class="elevation-1"
                >
                    <template v-slot:top>
                        <v-toolbar flat>
                            <v-toolbar-title>Clientes</v-toolbar-title>
                            <v-divider class="mx-4" inset vertical></v-divider>
                            <v-spacer></v-spacer>
                            <v-dialog v-model="CD">
                                <template v-slot:activator="{ on, attrs}">
                                    <v-btn color="success" dark class="mb-2" v-bind="attrs" v-on="on">Nuevo Cliente</v-btn>
                                </template>
                                <v-card>
                                    <v-card-title><span class="headline">{{ TituloFormulario }}</span></v-card-title>
                                    <v-card-text>
                                        <v-container>
                                            <v-row>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Nombres" label="Nombres"></v-text-field></v-col>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Apellidos" label="Apellidos"></v-text-field></v-col>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Direccion" label="Direccion"></v-text-field></v-col>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Telefono" label="Telefono"></v-text-field></v-col>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Nit" label="Nit"></v-text-field></v-col>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Adelanto" label="Adelanto"></v-text-field></v-col>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Debe" label="Debe"></v-text-field></v-col>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Observacion" label="Anotaciones"></v-text-field></v-col>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Foto" label="Foto"></v-text-field></v-col>
                                                <v-col cols="12" sm="6" md="4"><v-text-field v-model="CEdit.Activo" label="Activo"></v-text-field></v-col>
                                            </v-row>
                                        </v-container>
                                    </v-card-text>

                                    <v-card-actions>
                                        <v-spacer></v-spacer>
                                        <v-btn color="success" text @click="Guardar">Registrar</v-btn>
                                        <v-btn color="danger" text @click="Cerrar">Cancelar</v-btn>
                                    </v-card-actions>
                                </v-card>
                            </v-dialog>
                            <v-dialog v-model="CDD">
                                <v-card>
                                    <v-card-title class="headline">Desea macar como inactivo al cliente?</v-card-title>
                                    <v-card-actions>
                                        <v-spacer></v-spacer>
                                        <v-btn color="warning" text @click="ConfirmarBorrado">Si</v-btn>
                                        <v-btn color="danger" text @click="CerrarBorrar">Cancelar</v-btn>
                                        <v-spacer></v-spacer>
                                    </v-card-actions>
                                </v-card>
                            </v-dialog>
                        </v-toolbar>
                    </template>
                    <template v-slot: item.actions="{ item }">
                        <v-icon small class="mr-2" @click="EditarCliente(item)">mdi-pencil</v-icon>
                        <v-icon small @click="BorrarCliente(item)">mdi-delete</v-icon>
                    </template>
                    <template v:slot:no-data>
                        <v-btn color="success" @click="GetClientes">Actualizar</v-btn>
                    </template>
                </v-data-table>
            </v-card>
          </v-col>
      </v-row>
  </v-container>
</template>

<script>
import axios from "axios";
export default {
    data(){
        return{
            Buscar: '',
            CD: false,
            CDD: false,
            EditIndex: -1,
            
            CEdit:{
                IdCliente: 0,
                Nombres: '',
                Apellidos: '',
                Direccion: '',
                Telefono: '',
                Nit: '',
                Adelanto: 0,
                Debe: 0,
                Observcion: '',
                Foto: '',
                Activo: '',

            },
            CDef:{
                IdCliente: 0,
                Nombres: '',
                Apellidos: '',
                Direccion: '',
                Telefono: '',
                Nit: '',
                Adelanto: 0,
                Debe: 0,
                Observcion: '',
                Foto: '',
                Activo: '',

            },

            Encabezados: [
                { text: 'Cod.', align: 'start', sortable: false, value: 'IdCliente', },
                { text: 'Nombres', value: 'Nombres' },
                { text: 'Apellidos', value: 'Apellidos' },
                { text: 'Direccion', value: 'Direccion' },
                { text: 'Celular', value: 'Telefono' },
                { text: 'Nit', value: 'Nit' },
                { text: 'D. Adelantado', value: 'Adelanto' },
                { text: 'D. Restante', value: 'Debe' },
                { text: 'Anotaciones', value: 'Observaciones' },
                { text: 'Foto', value: 'Foto' },
                { text: 'Fecha de Registro', value: 'FC' },
                { text: 'Fecha de Modificacion', value: 'FE' },
            ],

            Clientes: [],
        }
    },

    methods:{
        async GetClientes(){
            try{
                axios.get(`http://192.168.1.4:3000/Clientes`).then(res=>{
                    this.Clientes = res.data;
                })
            } catch (e){
                console.log(e);
            }
        },

        EditarCliente (item) {
            this.EditIndex = this.Clientes.indexOf(item)
            this.CEdit = Object.assign({}, item)
            this.CDD = true
        },

        BorrarCliente (item) {
            this.EditIndex = this.Clientes.indexOf(item)
            this.CEdit = Object.assign({}, item)
            this.CCD = true
        },

        ConfirmarBorrado () {
            this.Clientes.splice(this.EditIndex, 1)
            this.CerrarBorrar()
        },

        Cerrar () {
            this.CD = false
            this.$nextTick(() => {
                this.CEdit = Object.assign({}, this.CDef)
                this.EditIndex = -1
            })
        },

        CerrarBorrar () {
            this.CDD = false
            this.$nextTick(() => {
                this.CEdit = Object.assign({}, this.CDef)
                this.EditIndex = -1
            })
        },

        Guardar () {
            if(this.EditIndex > -1) {
                Object.assign(this.Clientes[this.EditIndex], this.CEdit)
            } else {
                this.Clientes.push(this.CEdit)
            }
            this.Cerrar()
        }
    },

    created(){
        this.GetClientes();
    },

    computed: {
        TituloFormulario (){
            return this.EditIndex === -1 ? 'Registrar Cliente' : 'Editar Cliente'
        },
    },

    watch: {
        CD (val) {
            val || this.Cerrar()
        },
        CDD (val) {
            val || this.CerrarBorrar()
        }
    },
}
</script>