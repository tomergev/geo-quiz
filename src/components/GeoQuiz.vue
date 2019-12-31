<template>
  <div>Hello World</div>
</template>

<script>
export default {
  name: "GeoQuiz",
  data() {
    return {
      europeanCountries: []
    };
  },
  methods: {
    async fetch() {
      try {
        const res = await fetch(
          "https://restcountries.eu/rest/v2/region/europe"
        );
        const europeanCountries = await res.json();
        console.log(europeanCountries);
        this.europeanCountries = europeanCountries;
      } catch (err) {
        console.log(err);
      }
    }
  },
  created() {
    this.fetch();
  }
};
</script>

<style scoped>
</style>