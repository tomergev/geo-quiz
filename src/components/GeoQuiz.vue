<template>
  <div>Hello World</div>
</template>

<script>
export default {
  name: "GeoQuiz",
  data() {
    return {
      europeanCountriesArr: [],
      europeanCountriesObj: {},
      quizQuestions: []
    };
  },
  methods: {
    async fetchEuropeanCountries() {
      try {
        const res = await fetch(
          "https://restcountries.eu/rest/v2/region/europe"
        );
        const europeanCountriesArr = await res.json();

        const europeanCountriesObj = europeanCountriesArr.reduce(
          (acc, country) => {
            acc[country.name] = country;
            return acc;
          },
          {}
        );

        console.log({ europeanCountriesObj, europeanCountriesArr });
        this.europeanCountriesArr = europeanCountriesArr;
        this.europeanCountriesObj = europeanCountriesObj;
      } catch (err) {
        console.log(err);
      }
    },
    createQuizQuestions() {
      try {
        const generateRandomNumber = (min = 0, max = 52) => {
          min = Math.ceil(min);
          max = Math.floor(max);
          return Math.floor(Math.random() * (max - min + 1)) + min;
        };

        const quizQuestions = this.europeanCountriesArr.map(country => {
          const possibleChoices = [country.name];

          const choices = [country, {}, {}, {}].map((val, ind) => {
            if (!ind) return val;

            const selectCountry = () => {
              const randomIndex = generateRandomNumber();
              const randomCountry = this.europeanCountriesArr[randomIndex];

              if (
                possibleChoices.some(
                  countryName => randomCountry.name === countryName
                )
              ) {
                return selectCountry();
              } else {
                possibleChoices.push(randomCountry.name);
                return randomCountry;
              }
            };

            return selectCountry();
          });

          console.log(choices);
        });
      } catch (err) {
        console.log(err);
      }
    }
  },
  async created() {
    await this.fetchEuropeanCountries();
    await this.createQuizQuestions();
  }
};
</script>

<style scoped>
</style>