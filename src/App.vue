<template>
  <div id="app">
    <Header 
    v-bind:numCorrect="numCorrect"
    v-bind:numTotal="numTotal"
    />
    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="6" offset="3">
          <QuestionBox 
            v-if="questions.length"
            v-bind:currentQuestion="questions[index]"
            v-bind:next="next"
            v-bind:increment="increment"
            v-bind:numTotal="numTotal"
            v-bind:numCorrect="numCorrect"
          />
          <!-- Adding Skeleton UI until API is being fetched -->
          <b-jumbotron v-else>
            <b-skeleton animation="wave" width="85%"></b-skeleton>
            <b-skeleton animation="wave" width="70%"></b-skeleton>
            <hr class="my-4">
            <b-list-group>
              <b-list-group-item>
                <b-skeleton animation="wave" width="55%"></b-skeleton>
                <b-skeleton animation="wave" width="70%"></b-skeleton>                      
              </b-list-group-item>
              <b-list-group-item>
                <b-skeleton animation="wave" width="55%"></b-skeleton>
                <b-skeleton animation="wave" width="70%"></b-skeleton>                      
              </b-list-group-item>
              <b-list-group-item>
                <b-skeleton animation="wave" width="55%"></b-skeleton>
                <b-skeleton animation="wave" width="70%"></b-skeleton>                      
              </b-list-group-item>
              <b-list-group-item>
                <b-skeleton animation="wave" width="55%"></b-skeleton>
                <b-skeleton animation="wave" width="70%"></b-skeleton>                      
              </b-list-group-item>
            </b-list-group>
            
            <b-button variant="primary" disabled class="mt-3 mr-2">
              <b-spinner small></b-spinner>
              Submit <i class="fas fa-paper-plane"></i>
            </b-button>
            <b-button variant="success" disabled class="mt-3">
              <b-spinner small></b-spinner>
              Next <i class="fas fa-chevron-circle-right"></i>
            </b-button>
          </b-jumbotron>

        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import QuestionBox from './components/QuestionBox.vue'

export default {
  name: 'App',
  components: {
    Header,
    QuestionBox
  },
  data() {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 0
    }
  },
  methods: {
    next()  {
      this.index++;
    },
    increment(isCorrect)  {
      if (isCorrect)  {
        this.numCorrect++
      }
      this.numTotal++
    }
  },
  mounted: function() {
    fetch("https://opentdb.com/api.php?amount=10&category=17&type=multiple", {
      method: "GET"
    })
      .then((response) =>  {
        return response.json();
      })
      .then((jsonData) => {
        this.questions = jsonData.results
      })
  }
}
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