<template>
    <div>
        <div class="hero is-primary">
            <div class="hero-body">
                <p class="title">Quizzes</p>
            </div>
        </div>
        <div class="container">
            <div class="quizzes-container" v-if="this.display === 'Quiz'">
                <div class="quiz" v-for="quiz in quizzesData.quizzes"
                     :key="quiz.quiz_id">
                    <div class="quiz-title">
                        <li>{{quiz.quizTitle}}</li>
                    </div>
                    <div class="launch-button">
                        <button class="button" :id="quiz.quiz_id" @click="launchQuiz">
                            Launch Quiz
                        </button>
                    </div>
                    <div class="edit-button">
                        <button class="button" :id="quiz.quiz_id" @click="editQuiz">
                            Edit Quiz
                        </button>
                    </div>
                    <div class="remove-button">
                        <button class="button" :id="quiz.quiz_id" @click="removeQuiz">
                            Remove Quiz
                        </button>
                    </div>
                </div>
            </div>
            <div class="add-button" v-if="this.display === 'Quiz'">
                <button class="button" @click="addQuiz">
                    Add Quiz
                </button>
            </div>
            <Preview v-if="this.display === 'Preview'" 
                     :quizzesData="quizzesData" 
                     :quizId="quizId"
                     :environment="environment"
                     @closePreview="closePreview" />
            <Create v-if="this.display === 'Create'" 
                    :quizzesData="quizzesData"
                    @closeCreate="closePreview" />
        </div>
    </div>
</template>
<script>
    import Preview from './Preview.vue'
    import Create from './Create.vue'
    
    export default {
        name: 'quizzes',
        components: {
            Preview,
            Create
        },
        props: ['quizzesData'],
        data () {
            return {
                display: "Quiz",
                questionData: [],
                quizId: 0,
                environment: ""
            }
        },
        mounted(){
            console.log("quizzesData", this.quizzesData);
        },
        methods: {
            launchQuiz(event){
                this.quizId = parseInt(event.target.id);
                this.environment = "launch";
                this.display = "Preview";

            },
            editQuiz(event){
                for(var i=0; i < this.quizzesData.quizzes.length; i++){
                    if(this.quizzesData.quizzes[i].quiz_id === parseInt(event.target.id)){
                        this.questionData = this.quizzesData.quizzes[i];
                        this.quizId = parseInt(event.target.id);
                    }
                }
                this.environment = "edit";
                this.display = "Preview";
            },
            addQuiz(){
                this.display = "Create";
            },
            removeQuiz(event){
                var res = confirm("Are you sure you want to delete?");
                if (res == true) {
                    var quizzes = this.quizzesData.quizzes;
                    for(var i = 0; i < quizzes.length; i++){
                        if(quizzes[i].quiz_id === parseInt(event.target.id)){
                            quizzes.splice(i,1);
                        }
                    }
                    
                    quizzes.map((quiz, index) => {
                        quiz.quiz_id = index+1;
                    })
                }
            },
            closePreview(value){
                this.display = value;
            }
        }
    }
</script>
<style scoped>
    .container {
        border: solid 1px;
        margin-top: 10px;
        margin-bottom: 10px;
        padding: 5px;
    }
    .quizzes-container, .quiz-title {
        margin-top: 10px;
        margin-bottom: 10px;
    }
    .quiz {
        display: flex;
        padding: 5px;
/*        background-color: rgba(0, 209, 178, 0.1);*/
        background-color: #F1FAF4;
        margin-bottom: 5px;
    }
    .edit-button, .launch-button, .remove-button {
        margin: 0px 10px;
    }
</style>