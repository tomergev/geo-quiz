<template>
  <div class="flex-item">
    <img :src="`${country.flag}`" />
  </div>
</template>

<script>
export default {
  name: "FlagImage",
  props: ["country"]
};
</script>

<style scoped>
.flex-item {
  flex-basis: 50%;
}

img {
  border: 1px solid black;
  max-width: 100%;
  min-height: 100%;
}
</style>
