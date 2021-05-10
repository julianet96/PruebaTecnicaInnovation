<template>
  <div class="detail">
      <!-- Muestra los detalles -->
    <h2 class="detailInfo" v-for="detail in details" :key="detail[0]">{{detail[0]}} : {{detail[1]}}</h2>
    
    <!-- Vuelve a la pagina anterior -->
    <footer><router-link class="footer" :to="{path: 'data'}"> &lt; &lt; Go Badck</router-link></footer>
    

    <!-- Modal que muestra si ha salida satisfactoria la carga o si ha havido un error -->
    <modal v-if="showModal" @close="showModal = false">
    
        <h2 slot="header">{{message}}</h2>
    </modal>

  </div>
</template>
<script>
import modal from '../components/Modal.vue'
export default {
  data(){
    return {
     details:[],
     showModal:false,
     message:""
    }
  },
  name: 'detail',
  components: {
      modal
  },
  methods:{
      // Obtiene el detalle a partir de la url 
      getDetail(url){
           this.$axios
        .get(url)
        .then(Response => {
            this.transformDetail(Response.data)
            this.message = "Satisfactory loading"
            this.showModal = true;
        })
        .catch(err =>{
            this.message = "Error occurred reload the page"
            this.showModal = true;
        })
      },
      transformDetail(detail){
          let entries = Object.entries(detail)
          entries.forEach(element => {
              if(typeof element[1] == 'string' && element[0]!= "url" && element[0]!= "homeworld"){
                  this.details.push([element[0],element[1]])
              }
          })
      }
    
  },
  created(){
      // al crearse la pagina obten el detalle a partir de la variable guradada en el local storage
      this.getDetail(localStorage.dataId)
    
  }
}
</script>
<style scoped>
.detailInfo{
    text-align: left;
}

footer {
    margin-top: 20px;
    clear: both;
    position: relative;
    
}
.footer{
    color: black;
    text-decoration: none;
    font-size:31px;
}
</style>