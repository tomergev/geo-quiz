<template>
  <div class="container" v-if="finishedLoading">
    <FlagImage :country="`${quizQuestions[0].choices[0].flag}`" />
    <!-- <div class="flex-item">
      <img :src="`${quizQuestions[0].choices[0].flag}`" />
    </div>
    <div class="flex-item">
      <img :src="`${quizQuestions[0].choices[1].flag}`" />
    </div>
    <div class="flex-item">
      <img :src="`${quizQuestions[0].choices[2].flag}`" />
    </div>
    <div class="flex-item">
      <img :src="`${quizQuestions[0].choices[3].flag}`" />
    </div>-->
  </div>
</template>

<script>
import FlagImage from "./FlagImage";

export default {
  name: "GeoQuiz",
  data() {
    return {
      europeanCountriesArr: [],
      europeanCountriesObj: {},
      quizQuestions: [],
      finishedLoading: false,
    };
  },
  components: {
    FlagImage
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
          const possibleChoices = { [country.name]: true };

          const choices = [
            country,
            ...[{}, {}, {}].map(() => {
              const selectCountry = () => {
                const randomIndex = generateRandomNumber();
                const randomCountry = this.europeanCountriesArr[randomIndex];

                if (possibleChoices[randomCountry.name]) {
                  return selectCountry();
                } else {
                  possibleChoices[randomCountry.name] = true;
                  return randomCountry;
                }
              };

              return selectCountry();
            })
          ];

          return {
            choices,
            correctAnswer: country
          };
        });

        this.quizQuestions = quizQuestions;
        this.finishedLoading = true
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
.container {
  display: flex;
  flex-wrap: wrap;
}
</style>