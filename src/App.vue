<template>

  <div>

    <ScoreBoard :winCount="winCount" :loseCount="loseCount" />

    <template v-if="question">

      <h1 v-html="question">

      </h1>

      <div v-for="(answer,index) in answers" :key="index">

        <input :disabled="answerSubmitted"
               type="radio"
               name="options"
               :value="answer"
               v-model="chosenAnswer">
        <label v-html="answer"></label>

      </div>

      <button v-if="!answerSubmitted" class="send" type="button" @click="submitAnswer()">Send</button>

      <section v-if="answerSubmitted" class="result">
        <h4 v-if="chosenAnswer == correctAnswer"
            v-html="'&#9989; Congratulations, the answer &quot;' + correctAnswer + '&quot; is correct.'">
        </h4>
        <h4 v-else
            v-html="'&#10060; I´m sorry, you picked the wrong answer. The correct answer is &quot;' + correctAnswer + '&quot;.'">
        </h4>
        <button class="send" type="button" @click="getNewQuestion">Next question</button>
      </section>

    </template>

  </div>

</template>

<script>

import ScoreBoard from "@/components/ScoreBoard";

export default {
  name: 'App',
  components: {
    ScoreBoard
  },

  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
    }
  },

  methods: {
    submitAnswer: function () {
      if (!this.chosenAnswer) {
        alert("Pick one of the options");
      } else {
        this.answerSubmitted = true;
        if (this.chosenAnswer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },

    getNewQuestion: function () {

      this.answerSubmitted = false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      this.axios
          .get('https://opentdb.com/api.php?amount=1&category=15')
          .then((response) => {
            this.question = response.data.results[0].question;
            this.incorrectAnswers = response.data.results[0].incorrect_answers;
            this.correctAnswer = response.data.results[0].correct_answer;
          })
    }
  },

  computed: {
    answers() {
      let answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },

  created() {
    this.getNewQuestion()
  }

}

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

    input[type=radio] {
      margin: 12px 4px;
    }

    button.send {
      min-width: 120px;
      height: 45px;
      padding: 0 16px;
      background-color: royalblue;
      color: white;
      border: none;
      margin-top: 15px;
      cursor: pointer;
    }
}
</style>
