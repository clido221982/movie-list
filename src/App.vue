<template>
  <div id="app">
    <div class="contact-list">
      <h1>Movie List</h1>
      <table class="table">
        <thead>
          <tr>
            <th scope="col">No</th>
            <th scope="col">Title</th>
            <th scope="col">Year</th>
            <th scope="col">imdbID</th>
            <th scope="col">favourite</th>
          </tr>
        </thead>
        <tbody v-if="!loading">
          <tr>
            <th></th>
            <th>
              <input
                class="form-input"
                placeholder="search..."
                v-model="search"
              />
            </th>
            <th></th>
            <th></th>
            <th></th>
          </tr>
          <MovieList
            v-for="(item, index) in movielist"
            :key="index"
            :num="index"
            :item="item"
            :currentpage="currentpage"
            :addToList="addToList"
            :removeFromList="removeFromList"
            :favourite_list="favourite_list"
          />
        </tbody>
        <tbody v-else>
          <tr>
            <td colspan="5">loading...</td>
          </tr>
        </tbody>
      </table>
      <Pagination :setCurrentPage="setCurrentPage" :currentpage="currentpage" :pagecount="pagecount"/>
    </div>

    <div class="contact-list">
      <h1>Favourite List</h1>
      <table class="table">
        <thead>
          <tr>
            <th scope="col">No</th>
            <th scope="col">Title</th>
            <th scope="col">Year</th>
            <th scope="col">imdbID</th>
            <th scope="col">favourite</th>
          </tr>
        </thead>
        <tbody v-if="!loading">
          <MovieList
            v-for="(item, index) in favourite_display"
            :key="index"
            :num="index"
            :item="item"
            :currentpage="currentpage"
            :addToList="addToList"
            :removeFromList="removeFromList"
            :favourite_list="favourite_list"
          />
        </tbody>
        <tbody v-else>
          <tr>
            <td colspan="5">loading...</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import MovieList from "./components/MovieList.vue"
import Pagination from "./components/Pagination.vue"

import axios from 'axios';

import { domain, json_server } from './config';


@Options({
  components: {
    MovieList,
    Pagination
  },
  data () {
    return {
      loading: false,
      movielist: [],
      favourite_list: [],
      search: "",
      currentpage: 1,
      pagecount: 1
    }
  },
  methods: {
    getData: async function () {
      this.loading = true;
      let {data} = await axios.get(`${domain}/?Title=${this.search}&page=${this.currentpage}`);
      let data1 = await axios.get(json_server);

      this.movielist = data.data;
      this.pagecount = data.total_pages;
      this.favourite_list = data1.data;
      this.loading = false;
    },
    addToList: async function (event: any, item: any) {
      if(typeof item === "object") { // if this item isn't exist in favourite db, you will add item.
        fetch(json_server,{
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({...item, status: true})
        }).then(res => {
          this.getData();
          res.json();
        })
      } else { // if this item is already exist in favourite db, you will change this item.
        fetch(`${json_server}/${Math.abs(Number(item))}`,{
          method: 'PATCH',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({status: true})
        }).then(res => {
          this.getData();
          res.json();
        })
      }
    },
    removeFromList: async function (event: any, id: Number) {
      fetch(`${json_server}/${Number(id)}`,{
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({status: false})
      }).then(res => {
        this.getData();
        res.json();
      })
    },
    setCurrentPage: function (num: Number) {
      this.currentpage = num;
    }
  },
  computed: {
    favourite_display() {
      return this.favourite_list.filter((item: any) => item.status == true)
    }
  },
  async created() {
    let { data } = await axios.get(domain);
    let data1 = await axios.get(json_server);
    this.favourite_list = data1.data;

    this.movielist = data.data;
    this.pagecount = data.total_pages;
    this.loading = false;
  },
  watch: {
    search: function () {
      this.getData();
    },
    currentpage: function () {
      this.getData();
    }
  }
})
export default class App extends Vue {}
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    width: 60%;
    max-width: 900px;
    min-width: 300px;
    margin: 0 auto;
    margin-top: 60px;
  }

  .contact-list {
    margin-top: 30px;
  }
</style>
