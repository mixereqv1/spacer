<template>
    <div :class="[{ flexStart: step === 1 }, 'wrapper']" >
      <transition name="slide">
        <img src="./assets/logo.svg" class="logo" alt="" v-if="step === 1">
      </transition>
      <transition name="fade">
        <HeroImage v-if="step === 0" />
      </transition>
      <Claim v-if="step === 0" />
      <SearchInput v-model="searchValue" @input="handleInput" :dark="step === 1" />
      <div class="results" v-if="results && !loading && step === 1">
          <Item v-for="item in results" :item="item" :key="item.data[0].nasa_id"
          @click.native="handleModalOpen(item)" />
      </div>
      <div class="loader" v-if=" step === 1 && loading " ></div>
      <Modal v-if="modalOpen" :item="modalItem" @closeModal="modalOpen = false" />
    </div>
</template>
<script>
import axios from 'axios';
import debounce from 'lodash.debounce';
import Claim from '@/components/Claim.vue';
import SearchInput from '@/components/SearchInput.vue';
import HeroImage from '@/components/HeroImage.vue';
import Item from '@/components/Item.vue';
import Modal from '@/components/Modal.vue';

const baseURL = 'https://images-api.nasa.gov/search';

export default {
  name: 'Search',
  components: {
    Claim,
    SearchInput,
    HeroImage,
    Item,
    Modal,
  },
  data() {
    return {
      modalOpen: false,
      modalItem: null,
      loading: false,
      step: 0,
      searchValue: '',
      results: [],
    };
  },
  methods: {
    handleModalOpen(item) {
      this.modalOpen = true;
      this.modalItem = item;
    },
    // eslint-disable-next-line
    handleInput: debounce(function() {
      this.loading = true;
      console.log(this.searchValue);
      axios.get(`${baseURL}?q=${this.searchValue}&media_type=image`)
        .then((response) => {
          this.results = response.data.collection.items;
          this.loading = false;
          this.step = 1;
        })
        .catch((error) => {
          console.log(error);
        });
    }, 500),
  },
};
</script>
<style lang="scss" scoped>
  @import url('https://fonts.googleapis.com/css?family=Montserrat:300,400,600,800&display=swap');

  * {
    box-sizing: border-box;
    margin: 0;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  body {
    margin: 0;
    padding: 0;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .3s ease;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }

  .slide-enter-active, .slide-leave-active {
    transition: margin-top .3s ease;
  }
  .slide-enter, .slide-leave-to {
    margin-top: -50px;
  }

  .wrapper {
    position: relative;
    font-family: 'Montserrat', sans-serif;
    margin: 0;
    width: 100%;
    min-height: 100vh;
    padding: 30px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    &.flexStart {
      justify-content: flex-start;
    }

    .results {
      margin-top: 50px;
      display: grid;
      grid-template-columns: repeat(2,1fr);
      grid-gap: 20px;

      @media (min-width: 768px) {
        grid-template-columns: repeat(3,1fr);
      }
    }
  }

  .logo {
    position: absolute;
    top: 40px;
    left: 30px;
  }

  .loader {
  display: inline-block;
  width: 64px;
  height: 64px;
  margin-top: 50px;

  @media (min-width: 768px) {
    width: 90px;
    height: 90px;
  }
 }
.loader:after {
  content: " ";
  display: block;
  width: 46px;
  height: 46px;
  margin: 8px;
  border-radius: 50%;
  border: 6px solid #1e3d4a;
  border-color: #1e3d4a transparent #1e3d4a transparent;
  animation: loader 1.2s linear infinite;

  @media (min-width: 768px) {
    width: 90px;
    height: 90px;
  }
}
@keyframes loader {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

</style>
