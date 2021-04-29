<template>
    <div>
        <div class="title-container">
            <div class="create-title is-size-5"> Create </div>
            <div class="close-button">
                 <button class="button" value="save" @click="closeCreate">
                    Save &amp; Close
                </button>
                <button class="button" value="close" @click="closeCreate">
                    Close
                </button>
            </div>
        </div>
        <div class="container">
            <div class="create-container">
                <div class="quiz-title">
                    <p class="is-size-6 has-text-weight-bold">Quiz Title:</p>
                    <input type="text" name="quizTitle" size="50" v-model="quiz.quizTitle" >
                </div>
                <div class="question-list" v-if="quizId > 0">
                    <div v-for="question in quizzesData.quizzes[quizId-1].questions" :key="question.questionId">
                        {{question.questionId}}: {{question.questionText}} ({{question.questionType}})
                    </div>
                </div>
                <div class="select-question container">
                    <p>Add Question:</p>
                    <select class="comments-select-user" v-model="selected">
                      <option disabled value="">Please select a question type</option>
                      <option v-for="questionType in questionTypes" :key="questionType.id">{{questionType.type}}</option>
                    </select>
                    <button class="button" @click="getQuestion()">Add</button>
                </div>
                <div class="question-inputs container">
                    <CreateTrueFalse v-if="this.display === 'trueFalse'" 
                             :data="this.quizzesData"
                             :quizId="this.quizIndex"
                             @submitted="questionSubmitted" />

                    <CreateMultipleChoice v-if="this.display === 'mcq'" 
                                          :data="this.quizzesData" 
                                          :quizId="this.quizIndex" 
                                          @submitted="questionSubmitted" />

                    <CreateInput v-if="this.display === 'input'" 
                                 :data="this.quizzesData"
                                 :quizId="this.quizIndex"  
                                 @submitted="questionSubmitted"/>
                </div>
            </div>
        </div>
        
    </div>
</template>

<script>
    import CreateTrueFalse from '../components/createTrueFalse';
    import CreateMultipleChoice from '../components/createMultipleChoice';
    import CreateInput from '../components/createInput';
    
    import Vue from 'vue'
    //import axios from 'axios';
    import Toasted from 'vue-toasted';
    
    export default {
        name: 'create',
        components: {
            CreateTrueFalse,
            CreateMultipleChoice,
            CreateInput
        },
        props: ['quizzesData'],
        data(){
            return ({
                questionTypes: [
                    {
                        id: 1,
                        type: "trueFalse"
                    },
                    {
                        id: 2,
                        type: "input"
                    },
                    {
                        id: 3,
                        type: "mcq"
                    }
                ],
                quiz: {
                    questions: [],
                    quizTitle: "",
                    quiz_id: ""
                },
                quizIndex: 0,
                quizId: 0,
                selected: [],
                display: ""
            })
        },
        mounted(){
            Vue.use(Toasted);
            //console.log("quizzesData", this.quizzesData);
            this.quizIndex = this.quizzesData.quizzes.length + 1;
            this.quiz.quizTitle = "Quiz " + this.quizIndex;
            this.quiz.quiz_id = this.quizIndex;
            //console.log("quiz", this.quiz);
        },
        methods: {
            getQuestion(){
                this.display = this.selected;
            },
            questionSubmitted(question, quizId, submitQuestion){
                console.log("questionSubmitted", question, quizId, submitQuestion);
                if(submitQuestion){
                    this.quizId = quizId;
                    if (this.quizzesData.quizzes[quizId-1] !== undefined) {
                        this.quizzesData.quizzes[quizId-1].questions.push(question);
                    } else {
                        //console.log("question", quizId, question);
                        this.quiz.questions.push(question);

                        //console.log("quiz", this.quiz);
                        this.quizzesData.quizzes.push(this.quiz); 
                    }
                    
                    //console.log("Updated quizzesData", this.quizzesData);
//                Promise.all([ 
//                    axios
//                    .post('http://localhost:2020', this.quizzesData)
//                    .then((json) => {
//                        if(json.status === 200){
//                            this.display = "";
//                            //Show addition confirmation
//                            this.$toasted.show("Question added sucessfully.", { 
//                                     theme: "outline", 
//                                     position: "top-center", 
//                                     duration : 2000
//                                });
//                        }
//                    })
//                    .catch(err => {
//                        console.log("err", err)
//                    })
//                ])
                }
                console.log("Updated quizzesData", this.quizzesData);
                this.display = "";
            },
            closeCreate(){
                if(event.target.value === "save" && this.quiz.questions.length === 0){
                    this.$toasted.show("You must add at least one question", { 
                         type: "error",
                         theme: "bubble", 
                         position: "top-center", 
                         duration : 2000
                    });
                } else {
                    if(event.target.value === "close"){
                       this.$emit('closeCreate', 'Quiz');
                    } else {
                        console.log("save quiz");
                        this.$emit('closeCreate', 'Quiz');
                    }
                } 
            }
        }
    }
</script>
    
<style scoped>
    .title-container {
        display: flex;
        margin-top: 10px;
    }
    .create-title {
        padding-top: 5px;
    }
    button {
        margin: 0px 5px;
    }
    .question-list {
        padding: 10px;
    }
</style>