<template>
  <div class="endpoint">
    <!-- Titulo -->
    <h1>{{endpoint}}</h1>

    <!-- Filtro -->
    <h3>Filtro</h3>
    <input type="text" class="filter" v-model="search" @keyup="filter()">

    <!-- Lista de elementos -->
    <div class="menu" v-for="dataend in listItems" :key="dataend.url" @click="goToDetail(dataend.url)">
      <h2>{{dataend.name}}</h2>
    </div>

    <!-- Paginacion -->
    <div class="pagination">
      <button @click="changePage(pagination.currentPage - 1)" :disabled="pagination.currentPage == 0">❮</button>

      <select v-model="page" @change="changePage(page - 1)">
        <option v-for="pages in numPages" :key="pages" :value="pages">{{pages}}</option>
      </select>

      <button @click="changePage(pagination.currentPage + 1)" :disabled="pagination.currentPage == numPages.length - 1">❯</button>
    </div>

  <!-- Modal que muestra si ha salida satisfactoria la carga o si ha havido un error -->
  <modal v-if="showModal" @close="showModal = false">
    
    <h2 slot="header">{{message}}</h2>
  </modal>

    
    <!-- Link / boton que te devuelve atras en la pagina anterior -->
    <footer>
      <router-link class="footer" :to="{path: '/'}"> &lt; &lt; Go Back</router-link>
    </footer>

  </div>
</template>
<script>
import modal from '../components/Modal.vue'
export default {
  data(){
    return {
      endpoint:"",
      endpointData:[],
      pagination: {
        itemsPerPage: 5,
        currentPage: 0
      },
      listItems:[],
      filterItems:[],
      numPages:[],
      page:1,
      search:"",
      showModal:false,
      message:""
     
    }
  },
  name: 'endpoint',
  components: {
    modal
  },
  methods:{
    // funciona que guarda en local storage la url de la peticion de el detalle y te redirige a el detalle
    goToDetail(url){
      localStorage.dataId = url
      this.$router.push({ path: '/detail' })
    },
    // Funcion de filtrado a partir de la lista que se ha descargado filtramos por los valores que hay en el campo search
    filter(){
      this.filterItems = this.endpointData.filter(item => {
        return (""+ item.name).toLowerCase().includes(this.search.toLowerCase()) || !this.search
      })

      this.getNumPages()
      this.paginated()
    },
    //Funcion que se ejecuta cuando cambiamos de pagina de la lista (funcion de la paginacion) aqui le indicamos en que pagina esta
    changePage(page){

      this.pagination.currentPage = page

      this.page = this.pagination.currentPage + 1

      // lanzamos la funcion de paginado para que nos cambie la lista
      this.paginated()
    },
    //Obtencion de los datos a partir de una peticion api
    getEndpoint(endpoint){
      let endPointLowCase = endpoint.toLowerCase()
      this.$axios
        .get('https://swapi.dev/api/'+endPointLowCase)
        .then(Response => {
          console.log(Response)
          let data = Response.data.results
          this.endpointData = data
          this.filter()
          this.message = "Satisfactory loading"
          this.showModal = true
        })
        .catch(err =>{
          this.message = "Error occurred reload the page"
          this.showModal = true;
        })
    },
    //Cambia de pagina (hace un slice de los datos que hemos obtenidos de la api y lo hace a partir de la pagina en que estamos)
    paginated(){
      let index = this.pagination.currentPage * this.pagination.itemsPerPage;

      this.listItems = this.filterItems.slice(index, index + this.pagination.itemsPerPage)
    },
    //Resetea las variables de paginacion y obtiene el numero de paginas a partir del array filtrado 
    getNumPages(){
      this.numPages = [];
      this.page = 1;
      this.pagination.currentPage = 0

      let num = Math.ceil(this.filterItems.length / this.pagination.itemsPerPage)

      for(let i = 1; i <= num; i++){
        this.numPages.push(i)
      }
    }
    
  },
  created(){
    // Obtenemos la variable del local storage y lanzamos la funcion para obtener los datos a mostrar
    this.endpoint = localStorage.data
    this.getEndpoint(localStorage.data)
    
  }
}
</script>
<style scoped>
.menu{
  margin: 20px;
  border-style: solid;
  cursor: pointer;
}
.pagination {
  display: inline-block;
}

.pagination button {
  color: black;
  padding: 10px 16px;
  transition: background-color .3s;
  border: 1px solid #ddd;
  float: left;
  background-color: white;
}
.pagination select{
  color: black;
  padding: 10px 16px;
  border: 1px solid #ddd;
  float: left;
  transition: background-color .3s;
}
.filter{
  padding: 12px;
  border: 1px solid #ddd;
  width: 20%;
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
@media screen and (max-width: 800px) {
  .filter{
    width: 97%;
  }
}
</style>
