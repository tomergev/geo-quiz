<template>
  <div v-if="quizQuestions.length">
    <h2>{{quizQuestions[currentQuestionInd].find(correctAnswer).name}}'s flag?</h2>

    <div v-if="Object.keys(this.previouslySelectedAnswer).length">
      <h2 v-if="this.previouslySelectedAnswer.correctAnswer">Correct!</h2>
      <h2 v-else>That's incorrect, you chose {{this.previouslySelectedAnswer.name}}</h2>

      <button @click="nextQuestion">Next Question</button>
    </div>

    <div class="container">
      <div
        v-for="country in quizQuestions[currentQuestionInd]"
        :key="country.name"
        class="flex-item"
      >
        <FlagImage :country="country" />
      </div>
    </div>
  </div>
</template>

<script>
import FlagImage from "./FlagImage";

export default {
  name: "GeoQuiz",
  data() {
    return {
      quizQuestions: [],
      currentQuestionInd: 0,
      europeanCountriesArr: [],
      europeanCountriesObj: {},
      previouslySelectedAnswer: {}
    };
  },
  components: { FlagImage },
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

        const shuffleArray = array => {
          for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
          }

          return array;
        };

        const quizQuestions = this.europeanCountriesArr.map(country => {
          const possibleChoices = { [country.name]: true };

          const choices = [
            {
              ...country,
              correctAnswer: true
            },
            ...[{}, {}, {}].map(() => {
              const selectCountry = () => {
                const randomIndex = generateRandomNumber();
                const randomCountry = this.europeanCountriesArr[randomIndex];

                if (possibleChoices[randomCountry.name]) {
                  return selectCountry();
                } else {
                  possibleChoices[randomCountry.name] = true;
                  return {
                    ...randomCountry,
                    correctAnswer: false
                  };
                }
              };

              return selectCountry();
            })
          ];

          return shuffleArray(choices);
        });

        this.quizQuestions = shuffleArray(quizQuestions);
      } catch (err) {
        console.log(err);
      }
    },
    selectedAnswer(selectedCountry) {},
    correctAnswer(country) {
      if (country.correctAnswer) return country;
    },
    nextQuestion() {
      this.previouslySelectedAnswer = {};
      this.currentQuestionInd += 1;
    }
  },
  async created() {
    await this.fetchEuropeanCountries();
    await this.createQuizQuestions();
  },
  mounted() {
    this.$root.$on("on-selection", selectedCountry => {
      this.previouslySelectedAnswer = selectedCountry;
    });
  }
};
</script>

<style scoped>
.container {
  display: flex;
  flex-wrap: wrap;
}

.flex-item {
  flex-basis: 50%;
}
</style>