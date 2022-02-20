<template>
  <tr>
    <td>{{(currentpage - 1) * 10 + num + 1}}</td>
    <td>{{item.Title}}</td>
    <td>{{item.Year}}</td>
    <td>{{item.imdbID}}</td>
    <td>
      <i v-if="favourite === 0" class="bi bi-star" v-on:click="addToList($event, item)"></i>
      <i v-else-if="favourite < 0" class="bi bi-star" v-on:click="addToList($event, favourite)"></i>
      <i v-else class="bi bi-star-fill" v-on:click="removeFromList($event, favourite)"></i>
    </td>
  </tr>
</template>

<script>
export default {
  name: 'MovieList',
  props: {
    item: Object,
    num: Number,
    currentpage: Number,
    addToList: Function,
    removeFromList: Function,
    favourite_list: Array
  },
  computed: {
    favourite() {
      let temp = this.favourite_list.filter(a => a.imdbID === this.item.imdbID);
      if(temp.length > 0) {
        if(temp[0].status) {
          return temp[0].id;
        } else {
          return temp[0].id * -1;
        }
      } else {
        return 0;
      }
    }
  }
}
</script>
