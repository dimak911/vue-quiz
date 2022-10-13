<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template #lead>
        <p v-if="isQuizEnded">Last question</p>
        <h2 v-html="currentQuestion.question"></h2>
      </template>
      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="selectAnswer(index)"
          :class="[answerClass(index), { disablePointer: answered }]"
          v-html="answer"
        ></b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
        >Submit</b-button
      >
      <b-button
        @click="next"
        variant="success"
        :disabled="!answered || isQuizEnded"
        >Next</b-button
      >
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash';

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
    isQuizEnded: Boolean,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
    };
  },
  computed: {
    answers() {
      const answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      const answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;

      this.increment(isCorrect);
    },
    answerClass(index) {
      let answerClass = '';

      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected';
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct';
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = 'incorrect';
      }

      return answerClass;
    },
  },
};
</script>

<style lang="scss" scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item:hover {
  background-color: lightgrey;
  cursor: pointer;
}
.selected {
  background-color: lightblue;
}
.correct {
  background-color: lightgreen;
}
.incorrect {
  background-color: red;
}
.btn {
  margin: 0 5px;
}
.disablePointer {
  pointer-events: none;
}
</style>
