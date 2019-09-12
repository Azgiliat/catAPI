<template>
<div class="container__kitty-cell kitty-cell" v-bind:class="{bigkitty: info.isBig}">
  <img :src="info.url">
</div>
</template>


<script>
export default {
  data: function() {
    return {}
  },
  props: {
    info: {
      type: Object,
      required: true,
    }
  },
};
</script>

<style lang="scss">
img {
    width: 100%;
    height: 100%;
}

.loading .container__kitty-cell {
    display: none;
}
</style>
