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
            <button class="restart" @click="start_quiz, timeInterval">Continue</button>
        </div>
    </div>

    <!-- Quiz Box -->
    <div class="quiz_box" :class="{activeQuiz:quiz}">
        <header>
            <div class="title">Awesome Quiz Application</div>
            <div class="timer">
                <div class="time_left_txt">Time Left</div>
                <div class="timer_sec">{{ timedata }}</div>
            </div>
            <div id="time_line" class="time_line"></div>
        </header>
        <section>
            <div class="que_text">
                <!-- Here I've inserted question from JavaScript -->
                <div>{{ mainquestion }}</div>
            </div>
            <div class="option_list" >
                <div class="option" :class="[{disabled:disabled,incorrect:activeAnswerIndex == index,correct:option == correct_answer }]"  v-for="(option,index) in questions" :key="index" @click="choosevalue(option,index)">
                    <span :class="{correct:option == correct_answer }">
                        {{option}}
                    </span>
                    <div v-if="option == correct_answer" class="icon tick"><i class="fas fa-check"></i></div>
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
        correct_answer:'',
        activeAnswerIndex:-1,
        choosen:'',
        timedata: 15,
        linewidth: 549,
      }
  },
  mounted() {
      this.apicall();
      this.timeInterval();
  },
  methods: {
      timeInterval(){
         var timeoo = setInterval(() => {
              this.timedata--;
            //   document.getElementsByClassName("time_line").style.width = "100px";
              if (this.timedata == 0) {
                  this.timedata = 0;
                  clearInterval(timeoo)
              }
          }, 1000);
          var line = setInterval(() => {
                this.linewidth-- 
                document.getElementById("time_line").style.width = `${this.linewidth}px`;
                if (this.linewidth < 250) {
                        document.getElementById("time_line").style.background = "red"
                    }
                if (this.linewidth == 0) {
                    clearInterval(line)
                }
              }, 28.4);
      },
      choosevalue(ch_value,index){
        this.disabled = true;
        // document.getElementById("time_line").style.width = `0px`;
        // this.timedata = 0;
        if (ch_value !== this.correct_answer) {            
            this.activeAnswerIndex = index;
            this.choosen = this.correct_answer
        } else {
            console.log();
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
              console.log("======>",response);
          })
      }
  },
}
</script>

<style>

</style>
