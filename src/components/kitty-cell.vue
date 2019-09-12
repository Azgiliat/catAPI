<template>
<div class="container__kitty-cell kitty-cell" v-bind:class="{bigkitty: info.isBig}">
  <loader v-if="!loaded" />
  <img :src="info.url" @load="imgLoaded">
</div>
</template>


<script>
import loader from './loader.vue';
export default {
  data: function() {
    return {
      loaded: false,
    }
  },
  props: {
    info: {
      type: Object,
      required: true,
    }
  },
  components: {
    loader,
  },
  methods: {
    imgLoaded() {
      this.loaded = true;
    },
  },
};
</script>

<style lang="scss">
.kitty-cell {
    position: relative;
}

img {
    width: 100%;
    height: 100%;
}

.loading .container__kitty-cell {
    display: none;
}
</style>
