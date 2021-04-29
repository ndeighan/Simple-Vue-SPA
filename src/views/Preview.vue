<template>
    <div>
        <div class="title-container">
            <div class="preview-title is-size-5"> Edit - {{questionData.quizTitle}}</div>
        </div>
        
        <div class="container">
            <div class="edit-title">
                <p class="is-size-5 has-text-weight-bold">Quiz title:</p>
                <input type="text" v-model="questionData.quizTitle" rows="4" cols="50" placeholder="Insert question text." class="edit-title-input"/>
            </div>
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
            <button class="button" v-if="environment === 'launch' "
                    value="close" @click="closePreview">
                Close
            </button>
            <div class="close-button" v-else>
                <button class="button" value="save" @click="closePreview">
                    Save
                </button>
                <button class="button" value="close" @click="closePreview">
                    Close
                </button>
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
        <div class="modal-background"></div>
        <div class="edit-container" v-if="selectedQuestion[0].questionType.length > 0">
            <EditTrueFalse v-if="selectedQuestion[0].questionType === 'trueFalse'" 
                             :quizzesData="this.quizzesData" 
                             :quizId="this.quizId"  
                             :questionId="this.selectedQuestion[0].questionId"
                             :environment="environment"
                             @submitted="postData" />

            <EditMultipleChoice v-if="selectedQuestion[0].questionType === 'mcq'" 
                              :quizzesData="this.quizzesData" 
                              :quizId="this.quizId" 
                              :questionId="this.selectedQuestion[0].questionId"
                              :environment="environment"
                              @submitted="postData" />

            <EditInput v-if="selectedQuestion[0].questionType === 'input'" 
                              :quizzesData="this.quizzesData" 
                              :quizId="this.quizId"  
                              :questionId="this.selectedQuestion[0].questionId"
                              :environment="environment"
                              @submitted="postData" />
        </div>
    </div>
</template>

<script>
    import InputQuestion from '../components/InputQuestion';
    import TrueFalseQuestion from '../components/TrueFalseQuestion';
    import MultipleChoiceQuestion from '../components/MultipleChoiceQuestion';
    import EditTrueFalse from '../components/editTrueFalse';
    import EditMultipleChoice from '../components/editMultipleChoice';
    import EditInput from '../components/editInput';
    
    import Vue from 'vue'
    //import axios from 'axios';
    import Toasted from 'vue-toasted';
    
    
    export default {
        name: 'preview',
        components: {
            InputQuestion,
            TrueFalseQuestion,
            MultipleChoiceQuestion,
            EditTrueFalse,
            EditMultipleChoice,
            EditInput
        },
        props: ['quizzesData', 'quizId', 'environment'],
        data () {
            return {
                score: 0,
                scoreClicked: false,
                totalQuestions: 0,
                submittedAnswers: [],
                isComplete: false,
                questionData: [],
                quizIndex: 0,
                quizReport: [
                    {
                        isComplete: false,
                        score: 0
                    }
                ],
                selectedQuestion: [{
                    questionType: ""
                }],
                toast: [],
                modalBackground: document.getElementsByClassName("modal-background")
            }
        },
        mounted(){
            for(var i=0; i<this.quizzesData.quizzes.length; i++){
                if(this.quizzesData.quizzes[i].quiz_id === this.quizId){
                    this.questionData = this.quizzesData.quizzes[i]; 
                    this.quizIndex = i+1;
                }
            }
            
            console.log("Preview quizzesData", this.quizId, this.quizzesData);
            console.log("questionData", this.questionData);
            this.totalQuestions = this.questionData.questions.length;
            Vue.use(Toasted);
            
            this.quizIndex = this.quizzesData.quizzes.length + 1;
            
            Vue.nextTick(() => {
                if(this.environment === "launch"){
                    this.setupEnvironment();
                }
            })
            
        },
        methods: {
            setupEnvironment(){
                var x = document.getElementsByClassName("title-container");
                var y = document.getElementsByClassName("edit-title");
                var z = document.getElementsByClassName("remove-question");

                x[0].style.display = "none";
                y[0].style.display = "none";
                for(var i=0; i<z.length; i++){
                    z[i].style.display = "none";
                }
                
            },
            closePreview(event){
                if(event.target.value === "close"){
                   this.$emit('closePreview', 'Quiz');
                } else {
                    console.log("save quiz");
                }
                
            },
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
                this.modalBackground[0].style.display = "block";
                //console.log("questionId", this.selectedQuestion[0].questionId);
            },
            postData(value1, value2){
                console.log("value1", value1)
                console.log("value2", value2)
//                console.log("questionId", this.selectedQuestion[0].questionId)
                var selectedId = this.selectedQuestion[0].questionId;
                this.questionData.questions[selectedId-1] = value1;
                
                this.selectedQuestion = [{
                    questionType: ""
                }];
                this.modalBackground[0].style.display = "none";
//                console.log("questionData", this.questionData)
//                console.log("quizzesData", this.quizzesData)
//                Promise.all([ 
//                    axios
//                    .post('http://localhost:2020', this.questionData)
//                    .then((json) => {
//                        console.log("json", json);
//                        if(json.status === 200){
//                            this.selectedQuestion = [{
//                                questionType: ""
//                            }];
//                            //Show edit/removal confirmation
//                            if(value === "remove"){
//                                this.$toasted.show("Question removed sucessfully.", { 
//                                     theme: "outline", 
//                                     position: "top-center", 
//                                     duration : 2000
//                                });
//                            } else if(value === "edit"){
//                                this.$toasted.show("Question edited sucessfully.", { 
//                                     theme: "outline", 
//                                     position: "top-center", 
//                                     duration : 2000
//                                });
//                            }
//                        }
//                    })
//                    .catch(err => {
//                        console.log("err", err)
//                    })
//                ])
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
    .modal-background {
        display: none;
        background-color: #000000;
        opacity: 0.5;
    }
    .edit-container {
        min-width: 700px;
        position: absolute;
        top: 50px;
        left: 0px;
        background-color: deepskyblue;
        padding: 10px;
    }
    .title-container {
        display: flex;
        margin-top: 10px;
    }
    .preview-title {
        padding-top: 5px;
    }
    .edit-title {
        display: flex;
        margin: 10px 0px;
    }
    .edit-title-input {
        margin: 0px 5px;
    }
</style>