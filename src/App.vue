<script>
import axios from 'axios'

export default {
  data() {
    return {
      questions: [],        
      currentIndex: 0,     
      score: 0,            
      error: null,          
    }
  },

  mounted() {
    this.loadQuestions();
  },

  methods: {

    loadQuestions() {
      const apiUrl = 'https://opentdb.com/api.php?amount=10&category=27&type=multiple';

    axios.get(apiUrl)
      .then((response) => {
        const raw = response.data.results; 
        const formattedQuestions = [];

        for (let i = 0; i < raw.length; i++) {
          const q = raw[i];

          //copy the wrong answers
          const allAnswers = q.incorrect_answers.slice();

          // Insert the correct answer in a random place
          const randomIndex = Math.floor(Math.random() * 4);
          allAnswers.splice(randomIndex, 0, q.correct_answer);

          // Add a ready question to the list
          formattedQuestions.push({
            question: q.question,
            correct: q.correct_answer,
            shuffledAnswers: allAnswers,
          });
        }

        this.questions = formattedQuestions;
        this.currentIndex = 0;
        this.score = 0;
        this.error = null;
      })

      
      .catch((error) => {
        console.error('Error loading data:', error);
        this.error = 'Failed to load questions';
      });
    },
    selectAnswer(answer) {
      const correct = this.questions[this.currentIndex].correct;
      if (answer === correct) {
        this.score++;
      }
      this.currentIndex++;
    },

    restartQuiz() {
      this.loadQuestions();
    }
  }
}
</script>

<template>
  <div class="quiz">

    <div class="question-box">

      <div v-if="currentIndex < questions.length">
        <h2 v-html="questions[currentIndex].question"></h2>
      </div>

      <div v-else>
        <h2>Done!</h2>
        <p>Correct answers: {{ score }} / {{ questions.length }}</p>
        <button @click="restartQuiz" class="restart-btn">Start new</button>
      </div>

    </div>

    <div class="answers-row" v-if="currentIndex < questions.length">
      <div v-for="(answer, index) in questions[currentIndex].shuffledAnswers" :key="index">
        <button @click="selectAnswer(answer)">
          {{ answer }}
        </button>
      </div>
    </div>

    <div class="footer" v-if="currentIndex < questions.length">
      {{ currentIndex + 1 }} / {{ questions.length }}
    </div>

  </div>

</template>

<style>

html, body {
  height: 100%;
  margin: 0;
  background-color: #a4f0fa;
}

body {
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: Arial, sans-serif;
  height: 100vh; 
}

.quiz {
  width: 800px;
  margin-left: 12rem;
  padding: 2rem;
  border: 1px solid #000f50;
  background-color: rgb(227, 249, 255);
  box-shadow: 0 0 6px rgba(0, 27, 37, 0.1);
  display: flex;
  flex-direction: column;
}

.question-box {
  border: 1px solid #000f50;
  width: 100%;
  padding: 1rem;
  margin-bottom: 2rem;
  text-align: center;
  font-size: 1.4rem;
  color: #122f42;
}

.answers-row {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.answers-row button {
  margin: 0 0.5rem;
  padding: 0.6rem;
  width: 100%;
  cursor: pointer;
  font-size: 1rem;
  border-color: #000f50 ;
  background-color: #87d3ff ;
  color: #0b1116;
  transition: background-color 0.3s ease;
}

button:hover{
  background-color: #a4f0fa ;
}

.footer {
  position: absolute;
  bottom: 1rem;
  right: 1rem;
  font-size: 0.9rem;
  color: #1e2629;
}

.restart-btn {
  margin-top: 1rem;
  padding: 0.6rem;
  width: 15%;
  cursor: pointer;
  font-size: 1rem;
  border-color: #000f50 ;
  background-color: #87d3ff ;
  color: #0b1116;
  transition: background-color 0.3s ease;
}

@media only screen and (max-width: 1100px) {
  .quiz {
    margin-left: 0;
  }

  @media only screen and (max-width: 700px) {
  .quiz{
    width: 600px;
  }
  .question-box {
  width: 80%;
  padding: 0.8rem;
  margin-bottom: 2rem;
  text-align: center;
  font-size: 1.2rem;
  color: #122f42;
}
}

}
</style>