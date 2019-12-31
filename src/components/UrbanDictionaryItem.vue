<template>
  <div>
    <h3>{{uBItem.word}}</h3>
    <p>{{uBItem.definition}}</p>
  </div>
</template>

<script>
export default {
  name: "UrbanDictionaryItem",
  props: ["uBItem"]
};
</script>

<style scoped>
</style>