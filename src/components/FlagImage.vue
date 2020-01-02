<template>
  <div class="flex-item" @click="$emit('selected-answer', country)">
    <img :src="`${country.flag}`" />
  </div>
</template>

<script>
export default {
  name: "FlagImage",
  props: ["country"],
};
</script>

<style scoped>

img {
  border: 1px solid black;
  max-width: 100%;
  min-height: 100%;
}
</style>
