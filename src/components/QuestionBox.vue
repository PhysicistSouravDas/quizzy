<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template v-slot:lead>
                <!-- {{ currentQuestion.question }} -->
                <span v-html="currentQuestion.question"></span>
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item
                v-for="(answer, index) in shuffledAnswers"
                :key="index"
                @click="selectAnswer(index)"
                :class="answerClass(index)"
                :disabled="answered"
                >
                    <!-- {{ answer }} -->
                    <span v-html="answer"></span>
                </b-list-group-item>
            </b-list-group>

            <b-button 
            variant="primary"
            v-on:click="submitAnswer"
            :disabled="selectedIndex === null || answered"
            >
                Submit <i class="fas fa-paper-plane"></i>
            </b-button>
            <b-button v-if="numTotal < 10" v-on:click="next" variant="success" :disabled="(!answered)">
                Next <i class="fas fa-chevron-circle-right"></i>
            </b-button>
            <b-button v-else-if="numTotal === 10" variant="success" :disabled=true>
                Finished <i class="fas fa-ban"></i>
                {{ toast('b-toaster-bottom-right', true) }}
            </b-button>
            <!-- <div>
                <b-button @click="toast('b-toaster-bottom-right', true)" class="mb-2">View Score</b-button>
            </div> -->
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'
export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function,
        numCorrect: Number,
        numTotal: Number
    },
    data()  {
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false,
            // for bootstrap toast
            category: [
                "Bad",     // 0 marks
                "Poor",      //1-3 marks
                "Average",  //4-5 marks
                "Good",     //6-7 marks
                "Nice",     //8-9 marks
                "Best"      //10 marks
            ],
            catColor: [
                "danger",     // 0 marks
                "warning",      //1-3 marks
                "secondary",  //4-5 marks
                "success",     //6-7 marks
                "info",     //8-9 marks
                "primary"      //10 marks
            ],
            toastTitle: "",
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler()   {
                this.selectedIndex = null
                this.answered = false
                this.shuffleAnswers()
            }
        }
        
        // currentQuestion() {
        //     this.selectedIndex = null;
        //     this.shuffleAnswers();
        // }
    },
    methods: {
        selectAnswer(index)  {
            this.selectedIndex = index;
            console.log(index);
            },
        shuffleAnswers()    {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        submitAnswer()  {
            let isCorrect = false;
            if (this.selectedIndex === this.correctIndex) {
                isCorrect = true
            }
            this.answered = true
            this.increment(isCorrect)
        },
        answerClass(index)  {
            let answerClass = ''

            if (!this.answered && this.selectedIndex === index)   {
                answerClass = 'selected'
            } else if (this.answered && this.correctIndex === index)    {
                answerClass = 'correct'
            } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                answerClass = 'incorrect'
            }

            return answerClass
        },
        // Bootstrap Toast like notification for final score
        toast(toaster, append = false)  {
            if (this.numCorrect === 0) {
                this.toastTitle = this.category[0]
                this.reqdColor = this.catColor[0]
            } else if (this.numCorrect > 0 && this.numCorrect <= 3) {
                this.toastTitle = this.category[1]
                this.reqdColor = this.catColor[1]
            } else if (this.numCorrect > 3 && this.numCorrect <= 5)  {
                this.toastTitle = this.category[2]
                this.reqdColor = this.catColor[2]
            } else if (this.numCorrect > 5 && this.numCorrect <= 7)  {
                this.toastTitle = this.category[3]
                this.reqdColor = this.catColor[3]
            } else if (this.numCorrect > 7 && this.numCorrect <= 9) {
                this.toastTitle = this.category[4]
                this.reqdColor = this.catColor[4]
            } else if (this.numCorrect === 10)  {
                this.toastTitle = this.category[5]
                this.reqdColor = this.catColor[5]
            }
            console.log(this.reqdColor)
            this.reqdIndex = this.category.indexOf(this.toastTitle)

            this.$bvToast.toast(`You got ${this.numCorrect} out of ${this.numTotal}. Refresh to try again.`, {
                title: this.toastTitle,
                toaster: toaster,
                solid: true,
                variant: `${this.reqdColor}`,
                appendToast: append
            })
        }
    },
    computed: {
        answers() {
            let answers = [...this.currentQuestion.incorrect_answers];
            answers.push(this.currentQuestion.correct_answer);
            return answers;
        }
    }
}
</script>

<style scoped>
    .list-group {
        margin-bottom: 15px;
    }
    .list-group-item:hover {
        background: #eeeeee;
        cursor: pointer;
    }
    .btn {
        margin: 0 5px;
    }
    .selected {
        background-color: lightblue;
    }
    .correct {
        background-color: lightgreen;
    }
    .incorrect {
        background-color: red;
        color: white;
    }
</style>