<template>
    <div class="library">
        <div class="hero is-primary">
            <div class="hero-body">
                <p class="title">Quiz Library</p>
            </div>
        </div>
        <div class="container">
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
            </div>
        </div>
        <Preview v-if="this.display === 'Preview'" 
                     :quizzesData="quizzesData" 
                     :quizId="quizId"
                     :environment="environment"
                     @closePreview="closePreview" />
    </div>
</template>
<script>
    import Preview from './Preview.vue'
    
    export default {
        name: 'library',
        components: {
            Preview
        },
        props: ['quizzesData'],
        data(){
            return {
                environment: "launch",
                display: "",
                quizId: 0
            }
        },
        mounted(){
            console.log("library", this.quizzesData);
            
        },
        methods: {
            launchQuiz(event){
                console.log("launch quiz", event.target.id);
                this.quizId = parseInt(event.target.id);
                this.display = "Preview";
                let x = document.getElementsByClassName("container");
                x[0].style.display = "none";

            },
            closePreview(){
                console.log("Library Close");
                this.display = "";
                let x = document.getElementsByClassName("container");
                x[0].style.display = "block";
            }
        }
    }
</script>
<style scoped>
     .quizzes-container {
        margin-top: 10px;
    }
    .quiz-title {
        padding: 10px;
    }
    .quiz {
        display: flex;
        padding: 5px;
    }
    .launch-button {
        margin: 0px 10px;
    }
</style>