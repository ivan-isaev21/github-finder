<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container">

        <!-- error -->
        <div class="error" v-if="error" style="margin-bottom:20px ;">
          <p> {{ error }}</p>
        </div>

        <!-- search -->
        <search :value="search" placeholder="Type username ..." @search="search = $event" />

        <!-- buttons -->
        <button @click="getRepos" v-if="!repos" class="btn btnPrimary">Search!</button>    
        <button @click="getRepos"  v-if="repos" class="btn btnPrimary">Search Again!</button>    
        
        <!-- user -->
        <div class="user__wrapper" v-if="user">         
          <div class="user-img"> <img :src="user.avatar_url" alt="avatar" width="200" height="200"></div>
          
          <div class="user-body">          
            <p v-if="user.company">Company: {{ user.company }}</p>
            <p v-if="user.location">Location: {{ user.location }}</p>
            <p v-if="user.bio">Bio: {{ user.bio }}</p>
          </div>
        </div>

        <!-- wrapper -->
        <div class="repos__wrapper">

          <!-- item -->
          <div class="repos-item" v-for="repo in repos" :key="repo.id">
            <div class="repos-info">
              <a class="link" :href="repo.html_url" target="_blank"> {{ repo.name }}</a>
              <span> {{ repo.stargazers_count }} ⭐</span>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>
<script>
  import search from "@/components/Search.vue"
  import axios from 'axios'
  export default {
    components: {
      search
    },
    data() {
      return {
        user:null,
        search: '',
        error: null,
        repos: null
      }
    },
    methods:{
      getUser(){
        axios
        .get(`https://api.github.com/users/${this.search}`)
        .then(res => {
          this.user = res.data
        })
        .catch(error => {
          console.error(error)
          this.user = null
        })        
      },
      getRepos(){
        axios
        .get(`https://api.github.com/users/${this.search}/repos`)
        .then(res => {
          this.repos = res.data 
          this.error = null    
          this.getUser()    
        })
        .catch(error => {
          this.error = 'Can`t find this user'
          this.repos = null
          this.user = null
          console.error(error)
        })
  
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
  .repos__wrapper{
    width: 600px;
    margin: 30px 0;
  }
  .repos-info{
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
    padding: 10px 0;
    border-bottom: 1px solid #dbdbdb;    
  }
  .user__wrapper{
    display: flex;
    align-items: center;
    justify-content:space-evenly; 
    margin: 20px 0;   
  }
  .user-body{
    margin-left: 20px;
  }
</style>