<template>
    <div class="edit-input">
        <div>
            Edit Input
        </div>
        <hr />
        <div><p><strong>Question ID: </strong>{{inputQuestion.questionId}}</p></div>
        <hr />
        <div><p><strong>Question Type: </strong>{{inputQuestion.questionType}}</p></div>
        <hr />
        <div class="new-question-text">
            <p class="is-size-6 has-text-weight-bold">Question text:</p><br>
            <textarea type="text" name="body" v-model="inputQuestion.questionText" rows="4" cols="50" placeholder="Insert question text." />
        </div>
        <hr />
        <div class="new-question-answer">
            <p class="is-size-6 has-text-weight-bold">Answer text:</p><br>
            <textarea type="text" name="body" v-model="inputQuestion.answer" rows="4" cols="50" placeholder="Insert answer text." />
        </div>
        <hr />
        <div>
            <p class="is-size-6 has-text-weight-bold">Attempts:</p>
            <input type="number" name="attempts" size="50" v-model="inputQuestion.attempts">
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
        name: 'EditInput',
        props: ['quizzesData', 'quizId', 'questionId'],
        data(){
            return {
                inputQuestion: {
                    "questionId": this.questionId,
                    "questionType": "input",
                    "questionText": "",
                    "answer": "",
                    "isCorrect": false,
                    "attempts": 0
                },
                questionToEdit: {}
            }
        },
        mounted() {
            this.inputQuestion = this.quizzesData.quizzes[this.quizId-1].questions[this.questionId-1];
            //console.log("inputQuestion", this.inputQuestion);
        },
        methods: {
            submit(event){
                //console.log("edit inputQuestion submit", this.inputQuestion);
                this.inputQuestion.attempts = parseInt(this.inputQuestion.attempts);
                this.$emit('submitted', this.inputQuestion, this.quizId);

                event.preventDefault();
            }
        }
    }
</script>
<style scoped>

</style>