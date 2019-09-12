<template>
<div class="container" :class="{loading: !loaded}">
  <loader v-if="!loaded" />
  <kittyCell v-for="cell in cells" v-bind:key="cell.id" v-bind:info="cell" />
</div>
</template>

<script>
import kittyCell from './components/kitty-cell.vue';
import loader from './components/loader.vue';
export default {
  data: function() {
    return {
      cells: {},
      proms: [],
      loaded: false,
    }
  },
  components: {
    kittyCell,
    loader,
  },
  watch: {
    data() {
      return this.cells;
    },
  },
  methods: {
    getKitty: async function() {
      const URL = `https://api.thecatapi.com/v1/images/search`;
      const API_KEY = `35670411-1d12-4c56-8688-489a27765761`;
      const vm = this;
      for(let i = 0; i < 17; i++) {
        vm.proms.push(fetch(URL, {
            headers: {
              "x-api-key": API_KEY,
            },
          })
          .then((response) => {
            if(response.ok) {
              return response.json();
            } else {
              const err = new Error(`Bad response`);
              throw (err);
            }
          })
          .then((data) => {
            vm.$set(vm.cells, i, {
              id: i,
              isBig: false,
              url: data[0].url,
            });
          })
          .catch((error) => {
            vm.$set(vm.cells, i, {
              id: i,
              isBig: false,
              url: `error`,
            });
            console.error(error)
          }));
      }
    },
    selectBigKitty: function() {
      this.cells[Math.round(Math.random() * 16)].isBig = true;
    },
  },
  created: async function() {
    this.getKitty();
    Promise.all(this.proms)
      .then(() => {
        this.selectBigKitty();
        this.loaded = true;
      });
  },
};
</script>

<style lang="scss">
.container {
    width: 100%;
    height: 100%;
    display: grid;
    grid-template-columns: repeat(5, 20%);
    grid-template-rows: repeat(5, auto);
    grid-template-areas: ". . . . ." ". kitty kitty kitty ." ". kitty kitty kitty ." ". kitty kitty kitty ." ". . . . .";
}
.bigkitty {
    grid-area: kitty;
}

body {
    background-color: #eee;
}
</style>
