<template>
  <v-container>
      <v-row wrap class="mt-5">
          <v-col>
              
              <v-dialog v-model="Modal" persistent>
                  <template v-slot:activator="{ on, attrs}">
                      <v-btn color="green" dark v-bind="attrs" v-on="on">Registrar Cliente</v-btn>
                  </template>
                  <v-card>
                      <v-card-title>
                          <span class="headline" v-if="ModoEdicion">Editar Cliente</span>
                          <span class="headline" v-else>Registrar Cliente</span>
                      </v-card-title>
                      <v-card-text>
                          <v-container>
                              <v-row>
                                  <v-col cols="12" sm="6" md="4" v-if="ModoEdicion">
                                      <v-text-field prepend-icon="mdi-alpha-c-circle" label="Cod." disabled 
                                      v-model="Cliente.IdCliente" color="green"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4">
                                      <v-text-field prepend-icon="mdi-account" label="Nombres" required 
                                      v-model="Cliente.Nombres" color="green" maxlength="30"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4">
                                      <v-text-field prepend-icon="mdi-account" label="Apellidos" required 
                                      v-model="Cliente.Apellidos" color="green" maxlength="30"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4">
                                      <v-text-field prepend-icon="mdi-directions" label="Direccion" required
                                       v-model="Cliente.Direccion" color="green" maxlength="50"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4">
                                      <v-text-field prepend-icon="mdi-cellphone" label="Telefono" required
                                       v-model="Cliente.Telefono" color="green" type="number"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4">
                                      <v-text-field prepend-icon="mdi-file-document" label="NIT" required
                                       v-model="Cliente.Nit" color="green" maxlength="15"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4">
                                      <v-text-field prepend-icon="mdi-head-question-outline" label="Adelanto" required
                                       v-model="Cliente.Adelanto" color="green" type="number"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4">
                                      <v-text-field prepend-icon="mdi-head-question-outline" label="Debe" required
                                       v-model="Cliente.Debe" color="green" type="number"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4">
                                      <v-text-field prepend-icon="mdi-clipboard-text" label="Anotaciones" required
                                       v-model="Cliente.Observacion" color="green"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4">
                                      <v-text-field prepend-icon="mdi-card-account-phone" label="Foto" required
                                      v-model="Cliente.Foto" color="green"></v-text-field>
                                  </v-col>
                                  <v-col cols="12" sm="6" md="4" v-if="ModoEdicion">
                                      <v-checkbox prepend-icon="mdi-alpha-a-circle" label="Activo" required
                                      v-model="Cliente.Activo" color="green"></v-checkbox>
                                  </v-col>
                              </v-row>
                          </v-container>
                      </v-card-text>
                      <v-card-actions>
                          <v-spacer></v-spacer>
                          <v-btn v-if="ModoEdicion" color="green" text @click="Actualizar()">Actualizar <v-icon right>mdi-pencil</v-icon></v-btn>
                          <v-btn v-else color="green" text @click="Agregar()">Guardar <v-icon right>mdi-content-save</v-icon></v-btn>
                          <v-btn color="red" text @click="Limpiar">Cancelar <v-icon right>mdi-cancel</v-icon></v-btn>
                      </v-card-actions>
                  </v-card>
              </v-dialog>

              <v-simple-table fixed-header>
                  <template v-slot:default>
                      <thead>
                          <tr>
                              <th class="text-left">Cod.</th>
                              <th class="text-left">Nombre</th>
                              <th class="text-left">Direccion</th>
                              <th class="text-left">Telefono</th>
                              <th class="text-left">NIT</th>
                              <th class="text-left">Adelanto</th>
                              <th class="text-left">Debe</th>
                              <th class="text-left">Observacion</th>
                              <th class="text-left">Acciones</th>
                          </tr>
                      </thead>
                      <tbody>
                          <tr v-for="(item, index) in Clientes" :key="index">
                              <td>{{ item.IdCliente }}</td>
                              <td>{{ item.Nombres }} {{ item.Apellidos }}</td>
                              <td>{{ item.Direccion }}</td>
                              <td>{{ item.Telefono }}</td>
                              <td>{{ item.Nit }}</td>
                              <td>{{ item.Adelanto }}</td>
                              <td>{{ item.Debe }}</td>
                              <td>{{ item.Observacion }}</td>
                              <td>
                                  <v-icon small color="yellow" class="mr-2" @click="Editar(item)">mdi-pencil</v-icon>
                                  <v-icon small color="red" @click="Eliminar(item, index)">mdi-delete</v-icon>
                              </td>
                          </tr>
                      </tbody>
                  </template>
              </v-simple-table>
          </v-col>
      </v-row>
  </v-container>
</template>

<script>
import axios from "axios";
import alertify from "vue-alertify";
export default {
    data(){
        return{

            Clientes: [],
            Modal: false,
            ModoEdicion: false,
            
            Cliente:{
                IdCliente: 0,
                Nombres: '',
                Apellidos: '',
                Direccion: '',
                Telefono: '',
                Nit: '',
                Adelanto: 0,
                Debe: 0,
                Observacion: '',
                Foto: '',
                Activo: true,
            },
        }
    },

    created(){
        //Obtener Lista de clientes cuando se carga la vista de clientes
        this.GetClientes();
    },

    methods:{

        GetClientes(){
            try{
                axios.get(`http://192.168.1.4:3000/Clientes`).then(res=>{
                    this.Clientes = res.data;
                })
            } catch (e){
                console.log(e);
            }
        },

        Agregar(){
            if(this.Cliente.Nombres.length > 0 && this.Cliente.Apellidos.length > 0 && this.Cliente.Direccion.length > 0){
                if(this.Cliente.Nit.length == 0){this.Cliente.Nit = 'C/F'}
                var params = {
                    Nombres: this.Cliente.Nombres, Apellidos: this.Cliente.Apellidos,
                    Direccion: this.Cliente.Direccion, Telefono: this.Cliente.Telefono,
                    Nit: this.Cliente.Nit, Adelanto: this.Cliente.Adelanto,
                    Debe: this.Cliente.Debe, Observacion: this.Cliente.Observacion,
                    Foto: this.Cliente.Foto
                }
                axios.post(`http://192.168.1.4:3000/Clientes`, params).then(res=>{
                    this.GetClientes();this.Modal = false;
                    this.Limpiar();
                    this.$alertify.success('Cliente Registrado');
                }).catch(error=>{
                    this.$alertify.error(error);
                });
            }else{
                this.$alertify.error('Complete el formulario primero')
            }
        },
        
        Actualizar(){
             if(this.Cliente.Nombres.length > 0 && this.Cliente.Apellidos.length > 0 && this.Cliente.Direccion.length > 0){
                if(this.Cliente.Nit.length == 0){this.Cliente.Nit = 'C/F'}
                var params = {
                    Nombres: this.Cliente.Nombres, Apellidos: this.Cliente.Apellidos,
                    Direccion: this.Cliente.Direccion, Telefono: this.Cliente.Telefono,
                    Nit: this.Cliente.Nit, Adelanto: this.Cliente.Adelanto,
                    Debe: this.Cliente.Debe, Observacion: this.Cliente.Observacion,
                    Foto: this.Cliente.Foto, Activo: this.Cliente.Activo
                }
                axios.put(`http://192.168.1.4:3000/Clientes/${this.Cliente.IdCliente}`, params).then(res=>{
                    this.GetClientes();this.Modal = false;
                    this.Limpiar();
                    this.$alertify.success('Cliente Actualizado');
                }).catch(error=>{
                    this.$alertify.error(error);
                });
            }else{
                this.$alertify.error('Complete el formulario primero')
            }
        },

        Eliminar(item, index){
            this.$alertify.confirmWithTitle(
                'Inactivar Cliente',
                'Desea inactivar a ' + item.Nombres,
                () =>
                axios.delete(`http://192.168.1.4:3000/Clientes/${item.IdCliente}`)
                .then(()=>{
                    this.Clientes.splice(index, 1);
                    this.$alertify.success('Inactivacion Realizada');
                }).catch(error=>{
                    this.$alertify.error(error);
                }),
                () => this.$alertify.error('Inactivacion Cancelada')
            );
        },

        Editar(item){
            this.ModoEdicion = true;
            this.Cliente.IdCliente = item.IdCliente
            this.Cliente.Nombres = item.Nombres
            this.Cliente.Apellidos = item.Apellidos
            this.Cliente.Direccion = item.Direccion
            this.Cliente.Telefono = item.Telefono
            this.Cliente.Nit = item.Nit
            this.Cliente.Adelanto = item.Adelanto
            this.Cliente.Debe = item.Debe
            this.Cliente.Observacion = item.Observacion
            this.Cliente.Foto = item.Foto
            this.Modal = true;
        },

        Limpiar(){
            this.Modal = false
            this.ModoEdicion = false
            this.Cliente.IdCliente= 0
            this.Cliente.Nombres= ''
            this.Cliente.Apellidos= ''
            this.Cliente.Direccion= ''
            this.Cliente.Telefono= ''
            this.Cliente.Nit= ''
            this.Cliente.Adelanto= 0
            this.Cliente.Debe = 0
            this.Cliente.Observacion = ''
            this.Cliente.Foto = '',
            this.Cliente.Activo = true
        },
    },
}
</script>