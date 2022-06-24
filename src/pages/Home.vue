<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container">

        <!-- search -->
        <search :value="search" placeholder="Type username ..." @search="search = $event" />
        <button @click="getRepos" class="btn btnPrimary">Search!</button>

      </div>
    </section>
  </div>
</template>
<script>
  import search from "@/components/Search.vue"
  export default {
    components: {
      search
    },
    data() {
      return {
        search: ''
      }
    },
    methods:{
      getRepos(){
        console.log(`Get user ${this.search} repos`)
      }
    }
  }
</script>
<style lang="scss" scoped>
  .container {
    display: flex;
    align-items: center;
    flex-direction: column;
  }
  button{
    margin-top: 40px;
  }
</style>