<template>
  <ul class="pagination">
    <li class="page-item">
      <p class="page-link" href="#" v-on:click="setCurrentPage(currentpage - 1)">Previous</p>
    </li>

    <li v-if="currentpage > 4" class="page-item">
      <p v-on:click="setCurrentPage(1)" class="page-link" href="#">1</p>
    </li>
    <li v-if="currentpage > 4" class="page-item">
      <span class="page-link">...</span>
    </li>

    <li v-for="index in pageNum" :key="index" class="page-item">
      <p class="page-link" v-on:click="setCurrentPage(index)" href="#">{{index}}</p>
    </li>

    <li v-on:click="setCurrentPage(pagecount)" v-if="currentpage <= pagecount - 4" class="page-item">
      <span class="page-link">...</span>
    </li>
    <li v-if="currentpage <= pagecount - 4" class="page-item">
      <p v-on:click="setCurrentPage(pagecount)" class="page-link" href="#">{{pagecount}}</p>
    </li>

    <li class="page-item">
      <p class="page-link" href="#" v-on:click="setCurrentPage(currentpage)">Next</p>
    </li>
  </ul>
</template>

<script>
export default {
  name: 'Pagination',
  props: {
    setCurrentPage: Function,
    currentpage: Number,
    pagecount: Number
  },
  computed: {
    pageNum() {
      if (this.pagecount < 4) {
        return [...Array(this.pagecount + 1).keys()].slice(1);
      } else if (this.currentpage <= 4) {
        return [1, 2, 3, 4, 5];
      } else  if (this.currentpage > this.pagecount - 4) {
          return [...Array(5).keys()].reverse().map(v => this.pagecount - v);
      } else {
        return [this.currentpage -1, this.currentpage, this.currentpage + 1];
      }
    }
  }
}
</script>

<style type="text/css">
  .page-link {
    cursor: pointer;
  }
</style>