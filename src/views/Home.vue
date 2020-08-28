<template>
  <div class="container">
    <v-layout :wrap="true">
      <v-flex xs12>
        <v-card>
          <v-date-picker v-model="fecha"
          full-width=""
          locale="es-cl"
          :min="minimo"
          :max="maximo"
          @change="getDolar(fecha)"
          ></v-date-picker>
        </v-card>
        <v-card color="error" dark>
          <v-card-text class="display-1 text-center">
           {{valor}} - {{fecha}}
          </v-card-text>
        </v-card> 
      </v-flex>
    </v-layout>
    
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'
import {mapMutations} from 'vuex'
export default {
  name: 'Home',
  data(){
    return{
      fecha:new Date().toISOString().substr(0,10),
      minimo:'1984',
      maximo: new Date().toISOString().substr(0,10),
      valor : null,
    }
  },

  methods:{
    ...mapMutations(['mostrarLoading','ocultarLoading']),
    async getDolar(dia){
      let fecha = dia.split(['-'])
      let mmddyy = `${fecha[2]}-${fecha[1]}-${fecha[0]}`
      try {
         this.mostrarLoading({titulo:'Accediendo a información', color:'secondary'})
      let {data} = await axios.get(`https://mindicador.cl/api/dolar/${mmddyy}`)
      if(data.serie.length==0){
        this.valor='sin resultado'
      }else{
        this.valor = await data.serie[0].valor
      }
      } catch (error) {
        console.log(error)
      }finally{
        this.ocultarLoading()
      }
    }
  },

  created(){
    this.getDolar(this.fecha)
  }
  
}
</script>
