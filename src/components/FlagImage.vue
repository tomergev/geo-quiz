<template>
  <div
    v-bind:class="{'incorrect-answer': (responseState === 'incorrect'), 'correct-answer': (responseState === 'correct'), 'unclickable': !clickable}"
    class="flex-item"
    @click="onClick"
  >
    <img :src="`${country.flag}`" />
  </div>
</template>

<script>
export default {
  name: "FlagImage",
  props: ["country"],
  data() {
    return {
      clickable: true,
      responseState: "waiting" // Possible states: waiting, incorrect, correct
    };
  },
  methods: {
    onClick() {
      try {
        if (this.country.correctAnswer) {
          this.responseState = "correct";
        } else {
          this.responseState = "incorrect";
        }
      } catch (err) {
        console.log(err);
      } finally {
        this.$root.$emit("on-selection", this.country);
        // this.$emit("on-selection", this.country);
      }
    }
  },
  mounted() {
    this.$root.$on("on-selection", selectedCountry => {
      if (!selectedCountry.correctAnswer && this.country.correctAnswer) {
        this.responseState = "correct";
      }

      this.clickable = false;
    });
  }
};
</script>

<style scoped>
img {
  border: 1px solid black;
  max-width: 100%;
  min-height: 100%;
}

.incorrect-answer {
  border: 10px solid red !important;
}

.correct-answer {
  border: 10px solid green !important;
}

.unclickable {
  pointer-events: none;
}
</style>
