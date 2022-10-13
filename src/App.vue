<template>
  <div id="app">
    <HeaderBox :numCorrect="numCorrect" :numTotal="numTotal" />

    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="6" offset="3">
          <QuestionBox
            v-if="questions.length"
            :currentQuestion="questions[index]"
            :next="next"
            :increment="increment"
            :isQuizEnded="isQuizEnded"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import HeaderBox from './components/HeaderBox.vue';
import QuestionBox from './components/QuestionBox.vue';

export default {
  name: 'App',
  components: {
    HeaderBox,
    QuestionBox,
  },
  data() {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 0,
      isQuizEnded: false,
    };
  },
  methods: {
    next() {
      this.index++;

      if (this.index === this.questions.length - 1) {
        this.isQuizEnded = true;
      }
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.numCorrect++;
      }

      this.numTotal++;
    },
  },
  mounted() {
    fetch('https://opentdb.com/api.php?amount=10&type=multiple', {
      method: 'get',
    })
      .then(response => {
        return response.json();
      })
      .then(jsonData => {
        this.questions = jsonData.results;
      });
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
