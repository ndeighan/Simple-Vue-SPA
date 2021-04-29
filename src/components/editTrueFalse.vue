<template>
    <div class="edit-true-false">
        <div>
            Edit True/False
        </div>
        <hr />
        <div><p><strong>Question ID: </strong>{{trueFalseQuestion.questionId}}</p></div>
        <hr />
        <div><p><strong>Question Type: </strong>{{trueFalseQuestion.questionType}}</p></div>
        <hr />
        <div class="new-question-text">
            <p class="is-size-6 has-text-weight-bold">Question text:</p><br>
            <textarea type="text" name="body" v-model="trueFalseQuestion.questionText" rows="4" cols="50" placeholder="Insert question." />
        </div>
        <hr />
        <div>
            <p class="is-size-6 has-text-weight-bold">Answer: </p>
            <form ref="form" class="true-false-question">
                <label for="true" class="radio">           
                    <input type="radio" 
                           id="true" 
                           value=true 
                           v-model="trueFalseQuestion.answer">
                    True
                </label>
                <br>
                <label for="false" class="radio">
                    <input type="radio" 
                           id="false" 
                           value=false 
                           v-model="trueFalseQuestion.answer">
                    False
                </label>
                <br>
            </form>
        </div>
        <hr />
        <div>
            <p class="is-size-6 has-text-weight-bold">Attempts:</p>
            <input type="number" name="attempts" size="50" 
                   v-model="trueFalseQuestion.attempts">
        </div>
        <hr />
        <div>
            <p>Submit question to quiz</p>
            <a href="#" @click="submit" class="button">
                SUBMIT
            </a>
        </div>
    </div>
</template>
<script>
    export default {
        name: 'EditTrueFalse',
        props: ['quizzesData', 'quizId', 'questionId'],
        data(){
            return {
                trueFalseQuestion: {
                    "questionId": this.questionId,
                    "questionType": "trueFalse", //Question type
                    "questionText": "", //Question text
                    "answer": "true", //"true" or "false"
                    "attempts": 0, //attempts, 0 = infinite
                    "isCorrect": false
                },
                questionToEdit: {}
            }
        },
        mounted() {
            this.trueFalseQuestion = this.quizzesData.quizzes[this.quizId-1].questions[this.questionId-1];
            //console.log("trueFalseQuestion", this.trueFalseQuestion);
        },
        methods: {
            submit(event){
                //console.log("edit trueFalse submit", this.trueFalseQuestion);
                this.trueFalseQuestion.attempts = parseInt(this.trueFalseQuestion.attempts);
                this.$emit('submitted', this.trueFalseQuestion, this.quizId);
                event.preventDefault();
            }
        }
    }
</script>
<style scoped>

</style>