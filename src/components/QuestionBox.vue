<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">{{currentQuestion.question}}</template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >{{ answer }}</b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || isAnswer "
      >Submit</b-button>

      <b-button
        @click="next"
        variant="success"
        :disabled="!isAnswer"
        v-if="!islastQuestion"
      >Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
    islastQuestion: Boolean
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      correctIndex: null,
      isAnswer: false
    };
  },
  // computed: {
  //   answers() {
  //     let answers = [...this.currentQuestion.incorrect_answers];
  //     answers.push(this.currentQuestion.correct_answer);
  //     return answers;
  //   }
  // },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
      console.log(index);
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
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
      this.isAnswer = true;
      this.increment(isCorrect);
    },
    answerClass(index) {
      let answerClass = "";

      if (!this.isAnswer && this.selectedIndex === index) {
        answerClass = "selected";
      } else if (this.isAnswer && this.correctIndex === index) {
        answerClass = "correct";
      } else if (
        this.isAnswer &&
        this.correctIndex !== index &&
        this.selectedIndex === index
      ) {
        answerClass = "incorrect";
      }

      return answerClass;
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.isAnswer = false;
        this.shuffleAnswers();
      }
    }

    //currentQuestion () {
    //   this.selectedIndex = null;
    //   this.shuffleAnswers();
    // }
  }
  // ,
  // mounted() {
  //   this.selectedIndex = null;
  //   this.shuffleAnswers();
  //   console.log(this.currentQuestion);
  // }
};
</script>


<style scoped>
.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background: rgb(231, 228, 228);
  cursor: pointer;
}

.btn {
  margin: 0 5px 0 5px;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: lightcoral;
}
</style>


