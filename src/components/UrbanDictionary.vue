<template>
  <div>
    <div v-for="uBItem in randomDefs" v-bind:key="uBItem.word">
      <UrbanDictionaryItem v-bind:uBItem="uBItem" />
    </div>
  </div>
</template>

<script>
import UrbanDictionaryItem from "./UrbanDictionaryItem";

export default {
  name: "UrbanDictionary",
  components: {
    UrbanDictionaryItem
  },
  data() {
    return {
      randomDefs: []
    };
  },
  created() {
    this.fetch();
  },
  methods: {
    async fetch() {
      try {
        const res = await fetch("http://api.urbandictionary.com/v0/random");
        const randomDefs = await res.json();
        const randomDefsSorted = randomDefs.list.sort(
          (defA, defB) => defB.thumbs_up - defA.thumbs_up
        );
        console.log(randomDefsSorted);
        this.randomDefs = randomDefsSorted;
      } catch (err) {
        console.log(err);
      }
    }
  }
};
</script>

<style scoped>
</style>