<template>
<div class="container" :class="{loading: loading}">
  <div class="spinner">
    <div class="shadow"></div>
    <div class="mug"></div>
    <div class="cat">
      <ul class="eyes">
        <li></li>
        <li></li>
      </ul>
      <div class="mouth"></div>
    </div>
  </div>
  <!-- <p class="spinner-text">Котики грузятся</p> -->
  <kittyCell v-for="cell in cells" v-bind:key="cell.id" v-bind:info="cell" />
</div>
</template>

<script>
import kittyCell from './components/kitty-cell.vue';
export default {
  data: function() {
    return {
      cells: {},
      proms: [],
      loading: true,
      spinerSize: {
        width: ``,
        height: ``
      },
    }
  },
  components: {
    kittyCell
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
    this.spinerSize = {
      width: `${document.body.clientWidth / 10}px`,
      height: `${document.body.clientWidth / 10}px`,
    };
    this.getKitty();
    Promise.all(this.proms)
      .then(() => {
        this.selectBigKitty();
        this.loading = false;
      });
  },
};
</script>

<style lang="scss">
$mug1: #f4bbac;
$mug2: #f1dfcb;
$cat: #999;
$shadow: #a9a7a5;

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

.spinner {
    display: none;
    position: absolute;
    left: 50%;
    margin-left: -50px;
    top: 50%;
    margin-top: -40px;
}

.mug {
    position: absolute;
    width: 92px;
    height: 80px;
    background: repeating-linear-gradient(70deg, $mug1, $mug1 10px, $mug2 10px, $mug2 20px);
    border-radius: 20px 20px 50px 50px;
    border: 4px solid $mug1;

    &:after {
        content: "";
        position: absolute;
        width: 30px;
        height: 25px;
        border: 10px solid $mug1;
        border-radius: 100%;
        right: -28px;
        top: 10px;
        z-index: -1;
    }
}

.cat {
    width: 90px;
    height: 55px;
    background-color: $cat;
    position: absolute;
    border-radius: 250px 250px 120px 120px;
    left: 5px;
    top: -30px;
    box-shadow: 0 6px 0 1px rgba(244,187,172,1);
    z-index: 2;

    &:after,
    &:before {
        content: "";
        position: absolute;
        width: 0;
        height: 0;
        border-left: 18px solid transparent;
        border-right: 18px solid transparent;
        border-bottom: 25px solid $cat;
        top: -10px;
    }
    &:after {
        transform: rotate(-35deg);
    }
    &:before {
        transform: rotate(35deg);
        right: 0;
    }
}

.eyes li {
    position: absolute;
    list-style: none;
    float: left;
    width: 6px;
    height: 6px;
    background-color: #fff;
    border: 2px solid black;
    border-radius: 100%;

    &:first-child {
        right: 25px;
    }
    &:nth-child(2) {
        left: 25px;
    }

    &:after {
        content: "";
        position: absolute;
        height: 4px;
        width: 2px;
        background-color: black;
        top: -4px;
        left: -2px;
        transform: rotate(120deg);
    }
}

.mouth {
    position: absolute;
    left: 40px;
    top: 28px;

    &:after,
    &:before {
        content: "";
        position: absolute;
        width: 2px;
        height: 8px;
        background-color: black;
        transform: rotate(-55deg);
    }
    &:after {
        transform: rotate(55deg);
        right: -8px;
    }
}

.shadow {
    width: 90px;
    height: 30px;
    border-radius: 100%;
    background-color: $shadow;
    position: absolute;
    top: 70px;
    left: 6px;
    opacity: 0.5;
}

@keyframes jumping-kitty {
    0% {
        transform: translateY(0);
    }
    50% {
        transform: translateY(-100px);
    }
    100% {
        transform: tramslateY(0);
    }
}

.cat,
.mug {
    animation: jumping-kitty 2s infinite;
}

.loading .spinner {
    position: absolute;
    display: block;
    top: 50%;
    left: 50%;
    transform: translateX(-50%) translateY(-50%);
}
</style>
