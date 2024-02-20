<script>
  import CardList from './components/CardList.vue'
  import Header from './components/Header.vue'
  import AddCard from './components/AddCard.vue'
  import Footer from './components/Footer.vue'

  export default {
    components: {
      CardList,
      AddCard,
      Header,
      Footer
    },
    data() {
      return {
        apiData: {}
      }
    }, 
    methods:{
      updateApiData(newData) {
        this.apiData = newData
      }
    }
  }
</script>

<template>
  <Header />
  <main>
    <AddCard :apiData="apiData" @updateApiData="updateApiData"/>
    <CardList :apiData="apiData" @updateApiData="updateApiData" />
  </main>
  <Footer />
</template>

<style scoped>

</style>
