<template>
  <div class="endpoint">
    <h1>{{endpoint}}</h1>
    <h3>Filtro</h3>
    <input type="text" class="filter" v-model="search" @keyup="filter()">

    <div class="menu" v-for="dataend in listItems" :key="dataend.url" @click="goToDetail(dataend.url)">
      <h2>{{dataend.name}}</h2>
    </div>
    <div class="pagination">
      <button @click="changePage(pagination.currentPage - 1)" :disabled="pagination.currentPage == 0">❮</button>

      <select v-model="page" @change="changePage(page - 1)">
        <option v-for="pages in numPages" :key="pages" :value="pages">{{pages}}</option>
      </select>

      <button @click="changePage(pagination.currentPage + 1)" :disabled="pagination.currentPage == numPages.length - 1">❯</button>
    </div>

  <modal v-if="showSatisfactoryModal" @close="showSatisfactoryModal = false">
    
    <h2 slot="header">Satisfactory loading</h2>
  </modal>

  <modal v-if="showErrorModal" @close="showErrorModal = false">
    
    <h2 slot="header">Error occurred reload the page</h2>
  </modal>
    
    <router-link class="footer" :to="{path: '/'}"> &lt; &lt; Go Back</router-link>

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
      showSatisfactoryModal:false,
      showErrorModal:false
     
    }
  },
  name: 'endpoint',
  components: {
    modal
  },
  methods:{
    goToDetail(url){
      localStorage.dataId = url
      this.$router.push({ path: '/detail' })
    },
    filter(){
      this.filterItems = this.endpointData.filter(item => {
        return (""+ item.name).toLowerCase().includes(this.search.toLowerCase()) || !this.search
      })

      this.getNumPages()
      this.paginated()
    },
    changePage(page){

      this.pagination.currentPage = page

      this.page = this.pagination.currentPage + 1

      // lanzamos la funcion de paginado para que nos cambie la lista
      this.paginated()
    },
    getEndpoint(endpoint){
      let endPointLowCase = endpoint.toLowerCase()
      this.$axios
        .get('https://swapi.dev/api/'+endPointLowCase)
        .then(Response => {
          console.log(Response)
          let data = Response.data.results
          this.endpointData = data
          this.filter()
          this.showSatisfactoryModal = true
        })
        .catch(err =>{
          this.showErrorModal = true;
        })
    },
    paginated(){
      let index = this.pagination.currentPage * this.pagination.itemsPerPage;

      this.listItems = this.filterItems.slice(index, index + this.pagination.itemsPerPage)
    },
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
  padding: 12px 16px;
  border: 1px solid #ddd;
  float: left;
  transition: background-color .3s;
}
.filter{
  padding: 12px;
  border: 1px solid #ddd;
  width: 20%;
}
.footer{
    position:absolute;
    bottom: 5%;
    left:10%;
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
