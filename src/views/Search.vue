<template>
  <div class="wrapper">
    <div class="search">
      <label for="search">Search:</label>
      <input name="search" id="search" v-model="searchValue" @input="handleInput">
      <ul>
        <li v-for="item in results" :key="item.data[0].nasa_id">
          <p>{{ item.data[0].description }}</p>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
import axios from 'axios';
import debounce from 'lodash.debounce';

const baseURL = 'https://images-api.nasa.gov/search';

export default {
  name: 'Search',
  data() {
    return {
      searchValue: '',
      results: [],
    };
  },
  methods: {
    // eslint-disable-next-line
    handleInput: debounce(function() {
      axios.get(`${baseURL}?q=${this.searchValue}&media_type=image`)
        .then((response) => {
          this.results = response.data.collection.items;
        })
        .catch((error) => {
          console.log(error);
        });
    }, 500),
  },
};
</script>
<style lang="scss" scoped>
  .wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    margin: 0;
    padding: 30px;

    .search {
      width: 250px;
      display: flex;
      flex-direction: column;

      label {
        font-family: Montserrat, sans-serif;
      }

      input {
        height: 30px;
        border: 0;
        border-bottom: 1px solid #000;
      }
    }
  }
</style>
