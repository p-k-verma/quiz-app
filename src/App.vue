<template>
  <div id="app">
        <!-- start Quiz button -->
    <div class="start_btn"><button @click="start_quiz">Start Quiz</button></div>

    <!-- Info Box -->
    <div class="info_box" :class="{activeInfo:quiz}">
        <div class="info-title"><span>Some Rules of this Quiz</span></div>
        <div class="info-list">
            <div class="info">1. You will have only <span>15 seconds</span> per each question.</div>
            <div class="info">2. Once you select your answer, it can't be undone.</div>
            <div class="info">3. You can't select any option once time goes off.</div>
            <div class="info">4. You can't exit from the Quiz while you're playing.</div>
            <div class="info">5. You'll get points on the basis of your correct answers.</div>
        </div>
        <div class="buttons">
            <button class="quit" @click="exit">Exit Quiz</button>
            <button class="restart" @click="start_quiz">Continue</button>
        </div>
    </div>

    <!-- Quiz Box -->
    <div class="quiz_box" :class="{activeQuiz:quiz}">
        <header>
            <div class="title">Awesome Quiz Application</div>
            <div class="timer">
                <div class="time_left_txt">Time Left</div>
                <div class="timer_sec">15</div>
            </div>
            <div class="time_line"></div>
        </header>
        <section>
            <div class="que_text">
                <!-- Here I've inserted question from JavaScript -->
                <div>{{ mainquestion }}</div>
            </div>
            <div class="option_list" >
                <div class="option" :class="[{disabled:disabled, correct:correct}]"  v-for="(option,index) in questions" :key="index" @click="choosevalue(option)">
                    <span>
                        {{option}}
                    </span>
                </div>
            </div>
        </section>

        <!-- footer of Quiz Box -->
        <footer>
            <div class="total_que">
                <!-- Here I've inserted Question Count Number from JavaScript -->
            </div>
            <button class="next_btn">Next Que</button>
        </footer>
    </div>

    <!-- Result Box -->
    <div class="result_box">
        <div class="icon">
            <i class="fas fa-crown"></i>
        </div>
        <div class="complete_text">You've completed the Quiz!</div>
        <div class="score_text">
            <!-- Here I've inserted Score Result from JavaScript -->
        </div>
        <div class="buttons">
            <button class="restart">Replay Quiz</button>
            <button class="quit">Quit Quiz</button>
        </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  components: {
    
  },
  data() {
      return {
          quiz: false,
          questions:[],
          category:"",
          mainquestion:'',
          clickoption:'',
          disabled:false,
          correct:false,
          correct_answer:''
      }
  },
  created(){
      this.apicall();
  },
  methods: {
      choosevalue(ch_value){
        this.disabled = true;
        if (ch_value == this.correct_answer) {
            console.log("sahiiii h bhai");
            this.correct = true;
        }
      },
      start_quiz(){
          this.quiz = true
      },
      exit(){
          this.quiz = false
      },
      apicall(){
          axios
          .get('https://the-trivia-api.com/questions?limit=1')
          .then((response) => {
              this.mainquestion = response.data[0].question;
              this.correct_answer = response.data[0].correctAnswer;
              this.questions.push(response.data[0].correctAnswer);
              this.questions.push(...response.data[0].incorrectAnswers);
              this.questions.sort();
          })
      }
  },
}
</script>

<style>

</style>
