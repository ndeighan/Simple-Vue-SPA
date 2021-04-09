<template>
    <div>
        <div class="hero is-primary">
            <div class="hero-body">
                <p class="title">Preview</p>
            </div>
        </div>
        <div class="container">
            <div class="question-container" v-for="question in questionData.questions" :key="question.questionId">
                
                <div v-if="question.questionType === 'input'">
                    <div class="question-type">
                        <InputQuestion class="question-body" :question="question" @submitted="answerSubmitted" />
                        <div class="remove-question">
                            <button class="remove-question-button button" 
                                    :id="question.questionId"
                                    @click="removeQuestion">Remove Question</button>
                            <button class="edit-question-button button" 
                                    :id="question.questionId"
                                    @click="editQuestion">Edit Question</button>
                        </div>
                    </div>
                    <hr />
                </div>
                <div v-if="question.questionType === 'trueFalse'">
                    <div class="question-type">
                        <TrueFalseQuestion class="question-body" :question="question" @submitted="answerSubmitted" />
                        <div class="remove-question">
                            <button class="remove-question-button button" 
                                    :id="question.questionId"
                                    @click="removeQuestion">Remove Question</button>
                            <button class="edit-question-button button" 
                                    :id="question.questionId"
                                    @click="editQuestion">Edit Question</button>
                        </div>
                    </div>
                    <hr />
                </div>
                <div v-if="question.questionType === 'mcq'">
                    <div class="question-type">
                        <MultipleChoiceQuestion class="question-body" :question="question" @submitted="answerSubmitted" />
                        <div class="remove-question">
                            <button class="remove-question-button button" 
                                    :id="question.questionId"
                                    @click="removeQuestion">Remove Question</button>
                            <button class="edit-question-button button" 
                                    :id="question.questionId"
                                    @click="editQuestion">Edit Question</button>
                        </div>
                    </div>
                    <hr />
                </div>
            </div>
            <div class="score" v-if="isComplete">
                <button class="button" @click="submit" :disabled="scoreClicked">Click for Score</button>
                <div class="display-score">
                    <p class="score-title is-size-5 has-text-weight-bold" v-if="scoreClicked">
                        {{score}} out of {{totalQuestions}} ({{quizReport.score}}%)
                    </p>
                </div>
            </div>
        </div>
        <div class="edit-container" v-if="selectedQuestion[0].questionType.length > 0">
            <CreateTrueFalse v-if="selectedQuestion[0].questionType === 'trueFalse'" 
                             :data="this.selectedQuestion" 
                             @submitted="postData('edit')" />

            <CreateMultipleChoice v-if="selectedQuestion[0].questionType === 'mcq'" 
                                          :data="this.selectedQuestion" 
                                          @submitted="postData('edit')" />

            <CreateInput v-if="selectedQuestion[0].questionType === 'input'" 
                                          :data="this.selectedQuestion" 
                                          @submitted="postData('edit')" />
        </div>
    </div>
</template>

<script>
    import InputQuestion from '../components/InputQuestion';
    import TrueFalseQuestion from '../components/TrueFalseQuestion';
    import MultipleChoiceQuestion from '../components/MultipleChoiceQuestion';
    import CreateTrueFalse from '../components/createTrueFalse';
    import CreateMultipleChoice from '../components/createMultipleChoice';
    import CreateInput from '../components/createInput';
    
    import Vue from 'vue'
    import axios from 'axios';
    import Toasted from 'vue-toasted';
    
    
    export default {
        name: 'preview',
        components: {
            InputQuestion,
            TrueFalseQuestion,
            MultipleChoiceQuestion,
            CreateTrueFalse,
            CreateMultipleChoice,
            CreateInput
        },
        props: ['questionData'],
        data () {
            return {
                score: 0,
                scoreClicked: false,
                totalQuestions: 0,
                submittedAnswers: [],
                isComplete: false,
                quizReport: [
                    {
                        isComplete: false,
                        score: 0
                    }
                ],
                selectedQuestion: [{
                    questionType: ""
                }],
                toast: []
            }
        },
        mounted(){
            this.totalQuestions = this.questionData.questions.length;
            Vue.use(Toasted);
        },
        methods: {
            submit(){
                this.score = 0;
                
                for(var i = 0; i < this.totalQuestions; i++){
                   if(this.questionData.questions[i].isCorrect){
                       this.score++;
                   }
                }
                this.quizReport.isComplete = true;
                this.quizReport.score = this.score * 100 / this.totalQuestions;
                
                this.scoreClicked = true;
                //console.log("quizReport", this.quizReport);
            },
            answerSubmitted(value){
                
                if(!this.submittedAnswers.includes(value)){
                    this.submittedAnswers.push(value);
                }
                if(this.submittedAnswers.length === this.totalQuestions){
                    this.isComplete = true;
                }
            },
            removeQuestion(event){
                //Confirm deletion
                var res = confirm("Are you sure you want to delete?");
                if (res == true) {
                    //Return questions without the one selected
                    var newQuestions = JSON.parse(JSON.stringify(this.questionData.questions))
                    const amendedQuestions = newQuestions.filter(question => question.questionId != event.target.id);
                    //Reset questionIds
                    for(var i = 0; i < amendedQuestions.length; i++){
                        amendedQuestions[i].questionId = i+1;
                    }
                    //Update state
                    this.questionData.questions = amendedQuestions;
                    //axios put/post updated questionData to webserver 
                    this.postData("remove");
                }
                
            },
            editQuestion(event){
                this.selectedQuestion = [{questionType: ""}];
                const questionToEdit = parseInt(event.target.id);
                this.selectedQuestion = this.questionData.questions.filter(
                                        question => question.questionId === questionToEdit);
            },
            postData(value){
                Promise.all([ 
                    axios
                    .post('http://localhost:2020', this.questionData)
                    .then((json) => {
                        
                        if(json.status === 200){
                            this.selectedQuestion = [{
                                questionType: ""
                            }];
                            //Show edit/removal confirmation
                            if(value === "remove"){
                                this.$toasted.show("Question removed sucessfully.", { 
                                     theme: "outline", 
                                     position: "top-center", 
                                     duration : 2000
                                });
                            } else if(value === "edit"){
                                this.$toasted.show("Question edited sucessfully.", { 
                                     theme: "outline", 
                                     position: "top-center", 
                                     duration : 2000
                                });
                            }
                        }
                    })
                    .catch(err => {
                        console.log("err", err)
                    })
                ])
            }
        }
    }
</script>

<style scoped>
    .container {
        margin-top: 10px;
    }
    .score{
        display: inline-flex;
    }
    button {
        margin: 0px 5px;
    }
     .display-score {
        margin: 0px 5px;
         padding-top: 5px;
    }
    .question-type{
        display: flex;
        background-color: #F1FAF4;
        padding: 5px;
    }
    .question-body {
        text-align: left;
        width: 50%;
    }
    .remove-question {
        text-align: right;
        width: 50%;
    }
    .edit-container {
        min-width: 700px;
        position: absolute;
        top: 235px;
        left: 50px;
        background-color: deepskyblue;
        padding: 10px;
    }
</style>