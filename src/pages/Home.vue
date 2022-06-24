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
        <button @click="getRepos" v-if="repos" class="btn btnPrimary">Search Again!</button>

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
        <div class="repos__wrapper" v-if="repos">

          <!-- item -->
          <div class="repos-info">
            <span @click="sort('name')">Name {{sortIcon('name')}}</span>
            <span @click="sort('stargazers_count')">Star {{sortIcon('stargazers_count')}}</span></div>
          <div class="repos-item" v-for="repo in reposSort" :key="repo.id">
            <div class="repos-info">
              <a class="link" :href="repo.html_url" target="_blank"> {{ repo.name }}</a>
              <span> {{ repo.stargazers_count }} ⭐</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- buttons -->
    <section v-if="repos">
      <div class="container">
        <div class="button-list">
          <div @click="prevPage" class="btn btnPrimary">←</div>
          <div @click="nextPage" class="btn btnPrimary">→</div>
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
        user: null,
        search: '',
        currentSort: 'name',
        currentSortDir: 'desc',
        page: {
          current: 1,
          length: 3
        },
        error: null,
        repos: null
      }
    },
    computed: {
      reposSort() {
        return this.repos.sort((a, b) => {
          let mod = 1
          if (this.currentSortDir === 'desc') mod = -1
          if (a[this.currentSort] < b[this.currentSort]) return -1 * mod
          if (a[this.currentSort] > b[this.currentSort]) return 1 * mod
          return 0
        }).filter((row, index) => {
          let start = (this.page.current - 1) * this.page.length
          let end = this.page.current * this.page.length
          if (index >= start && index < end) return true;
        })
      }
    },
    methods: {
      getUser() {
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
      getRepos() {
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
      },
      //sort
      sort(e) {
        if (e === this.currentSort) {
          this.currentSortDir = this.currentSortDir === 'asc' ? 'desc' : 'asc'
        }
        this.currentSort = e
      },
      //pagination
      nextPage() {
        if ((this.page.current * this.page.length) < this.repos.length) {
          this.page.current += 1
        }
      },
      prevPage() {
        if (this.page.current > 1) {
          this.page.current -= 1
        }
      },
      sortIcon(e) {
        if (e === this.currentSort && this.currentSortDir === 'desc') {
          return '↑'
        }
        return '↓'
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

  button {
    margin-top: 40px;
  }

  .repos__wrapper {
    width: 600px;
    margin: 30px 0;
  }

  .repos-info {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
    padding: 10px 0;
    border-bottom: 1px solid #dbdbdb;
  }

  .user__wrapper {
    display: flex;
    align-items: center;
    justify-content: space-evenly;
    margin: 20px 0;
  }

  .user-body {
    margin-left: 20px;
  }

  .button-list {
    width: 100%;
    text-align: center;
  }  
</style>