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
                    <div v-show="test" :class="['icon',{tick:correct_answer == option}]"><i v-show="correct_answer == option" class="fa fa-check" aria-hidden="true"></i></div>
                    <div v-show="activeAnswerIndex == index" class="icon cross"><i class="fa fa-times" aria-hidden="true"></i></div>
                </div>
            </div>
        </section>

        <!-- footer of Quiz Box -->
        <footer>
            <div class="total_que">
                <span class="desktop">Your Score is {{totalcorrect}} of {{ totaldone }} Questions</span>
                <span class="mobile">Your Score is <br> {{totalcorrect}} of {{ totaldone }} Questions</span>
                
            </div>
            <button class="next_btn" :class="{show:nextque}" @click='next_question'>Next Que</button>
        </footer>
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
        linewidth: 100,
        totalcorrect: 0,
        totaldone: 0,
        nextque: false,
        firstinterval: null,
        secondinterval: null,
        ticking: false,
        checkinganswer: -1,
        startquizclass: false,
        test: false,
      }
  },
  mounted() {
      
  },
  methods: {
      startquiz(){
          this.addinbox = false;
          this.startquizclass = true;
          this.apicall();
        //   this.timeInterval();
      },
      next_question() {
          this.questions = [];
          this.activeAnswerIndex = -1;
          this.disabled = false;
          this.timedata = 15;
          this.linewidth = 100;
          this.checkinganswer = -1,
          this.apicall();
        //   this.timeInterval();
          this.mainquestion = ''
          document.getElementById("time_line").style.background = "#32723d"
          this.nextque= false;
          this.test = false;
      },
      timeInterval(){
         this.firstinterval = setInterval(() => {
              this.timedata--;
              if (this.timedata == 0) {
                  this.timedata = 0;
                  this.disabled = true;
                  this.ticking = true;
                    this.nextque = true;
                    // this.correct = true;
                    this.test = true;
                  clearInterval(this.firstinterval)
              }
          }, 1000);
          this.secondinterval = setInterval(() => {
              console.log(this.linewidth);
                this.linewidth = this.linewidth - (6.666666666666667);
                if(this.linewidth < 0){
                    this.linewidth = 0;
                } 
                document.getElementById("time_line").style.width = `${this.linewidth}%`;
                if (this.linewidth < 20) {
                        document.getElementById("time_line").style.background = "#e25933"
                    }
                if (this.linewidth == 0) {
                    clearInterval(this.secondinterval)
                }
              }, 1000);
      },
      choosevalue(ch_value,index){
        this.ticking = true;
        this.disabled = true;
        this.nextque = true;
        this.correct = true;
        this.test = true;
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
              this.timeInterval();
          })
      }
  },
}
</script>

<style>

</style>
