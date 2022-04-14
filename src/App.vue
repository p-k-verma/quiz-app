<template>
  <div id="">
        <!-- start Quiz button -->
    <div class="start_btn"><button @click="start_quiz">Start Quiz</button></div>

    <!-- Info Box -->
    <div class="info_box" :class="{openinfo:addinbox}">
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
            <button class="restart" @click="startquiz">Continue</button>
        </div>
    </div>

    <!-- Quiz Box -->
    <div class="quiz_box"  :class="{quizclass:startquizclass}">
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
                <div class="option" :class="[{disabled:disabled,incorrect:activeAnswerIndex == index,correct:checkinganswer == index }]"  v-for="(option,index) in questions" :key="index" @click="choosevalue(option,index)">
                    <span>
                        {{option}}
                    </span>
                    <div v-if="checkinganswer == index" class="icon" :class="{tick:checkinganswer == index}"><i class="fa fa-check" aria-hidden="true"></i></div>
                    <div v-if="activeAnswerIndex == index" class="icon cross"><i class="fa fa-times" aria-hidden="true"></i></div>
                </div>
            </div>
        </section>

        <!-- footer of Quiz Box -->
        <footer>
            <div class="total_que">
                <span>Your Score is <br><p> {{totalcorrect}}</p> of <p>{{ totaldone }}</p> Questions</span>
            </div>
            <button class="next_btn" :class="{show:nextque}" @click='next_question'>Next Que</button>
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
          addinbox: false,
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
        totalcorrect: 0,
        totaldone: 0,
        nextque: false,
        firstinterval: null,
        secondinterval: null,
        ticking: false,
        checkinganswer: -1,
        startquizclass: false,
      }
  },
  mounted() {
      
  },
  methods: {
      startquiz(){
          this.addinbox = false;
          this.startquizclass = true;
          this.apicall();
          this.timeInterval();
      },
      next_question() {
          this.questions = [];
          this.activeAnswerIndex = -1;
          this.disabled = false;
          this.timedata = 15;
          this.linewidth = 549;
          this.checkinganswer = -1,
          this.apicall();
          this.timeInterval();
          this.mainquestion = ''
      },
      timeInterval(){
         this.firstinterval = setInterval(() => {
              this.timedata--;
              if (this.timedata == 0) {
                  this.timedata = 0;
                  clearInterval(this.firstinterval)
              }
          }, 1000);
          this.secondinterval = setInterval(() => {
                this.linewidth-- 
                document.getElementById("time_line").style.width = `${this.linewidth}px`;
                if (this.linewidth < 250) {
                        document.getElementById("time_line").style.background = "#e25933"
                    }
                if (this.linewidth == 0) {
                    clearInterval(this.secondinterval)
                }
              }, 28.4);
      },
      choosevalue(ch_value,index){
        this.ticking = true;
        this.disabled = true;
        this.nextque = true;
        clearInterval(this.firstinterval)
        clearInterval(this.secondinterval)
        if (ch_value !== this.correct_answer) {            
            this.activeAnswerIndex = index;
            this.choosen = this.correct_answer;
            this.totaldone++;
        } else {
            this.checkinganswer = index;
            this.totaldone++;
            this.totalcorrect++;
        }
      },
      start_quiz(){
          this.quiz = true;
          this.addinbox = true;
      },
      exit(){
          this.quiz = false;
          this.addinbox = false;
      },
     async apicall(){
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
