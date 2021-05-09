<template>
  <div class="detail">
    <h2 class="detailInfo" v-for="detail in details" :key="detail[0]">{{detail[0]}} : {{detail[1]}}</h2>
    
    <router-link class="footer" :to="{path: 'data'}"> &lt; &lt; Go Back</router-link>

    <modal v-if="showSatisfactoryModal" @close="showSatisfactoryModal = false">
    
        <h2 slot="header">Satisfactory loading</h2>
    </modal>

    <modal v-if="showErrorModal" @close="showErrorModal = false">
        
        <h2 slot="header">Error occurred reload the page</h2>
    </modal>

  </div>
</template>
<script>
import modal from '../components/Modal.vue'
export default {
  data(){
    return {
     details:[],
     showSatisfactoryModal:false,
     showErrorModal:false
    }
  },
  name: 'detail',
  components: {
      modal
  },
  methods:{
      getDetail(url){
           this.$axios
        .get(url)
        .then(Response => {
            this.transformDetail(Response.data)
            this.showSatisfactoryModal = true;
        })
        .catch(err =>{
            this.showErrorModal = true;
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
      this.getDetail(localStorage.dataId)
    
  }
}
</script>
<style scoped>
.detailInfo{
    text-align: left;
}
.footer{
    position:absolute;
    bottom: 5%;
    left:10%;
    color: black;
    text-decoration: none;
    font-size:31px;
}
</style>