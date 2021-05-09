<template>
  <div class="home">
    <h1 class="titel">Titulo</h1>
    
    <div class="menu" @click="goData('People')">
      <h1>People</h1>
    </div>
    
    <div class="menu" @click="goData('Starships')">
      <h1>Starships</h1>
    </div>
    
    <div class="menu" @click="goData('Planets')">
      <h1>Planets</h1>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return {
     
    }
  },
  methods:{
    goData(type){
      localStorage.data = type;
      this.$router.push({ path: '/data' })
    }
  },
  name: 'Home',
  components: {

  },
  
}
</script>
<style scoped>
.menu{
  margin: 20px;
  border-style: solid;
  cursor: pointer;
}
.home a{
  text-decoration: none;
  color:black;
}
.menu:hover{
  background-color: lightgray;
}
.titel{
  text-align: left;
  margin: 20px;
}
</style>