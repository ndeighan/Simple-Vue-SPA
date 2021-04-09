<template>
    <div class="true-false-inputs" v-bind="inputQuestion">
        <div>
            {{cardTitle}}Input
        </div>
        <hr />
        <div><p><strong>Question ID: </strong>{{inputQuestion[0].questionId}}</p></div>
        <hr />
        <div><p><strong>Question Type: </strong>{{inputQuestion[0].questionType}}</p></div>
        <hr />
        <div class="new-question-text">
            <p class="is-size-6 has-text-weight-bold">Question text:</p><br>
            <textarea type="text" name="body" v-model="inputQuestion[0].questionText" rows="4" cols="50" placeholder="Insert question text." />
        </div>
        <hr />
        <div class="new-question-answer">
            <p class="is-size-6 has-text-weight-bold">Answer text:</p><br>
            <textarea type="text" name="body" v-model="inputQuestion[0].answer" rows="4" cols="50" placeholder="Insert answer text." />
        </div>
        <hr />
        <div>
            <p class="is-size-6 has-text-weight-bold">Attempts:</p>
            <input type="number" name="attempts" size="50" v-model="inputQuestion[0].attempts">
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
        name: 'CreateInput',
        props: ['data'],
        data(){
            return {
                inputQuestion: [{
                    "questionId": 0,
                    "questionType": "input",
                    "questionText": "",
                    "answer": "",
                    "isCorrect": false,
                    "attempts": 0
                }],
                cardTitle: ""
            }
        },
        mounted(){
            if(this.data.questions){
                this.inputQuestion[0].questionId = this.data.questions.length+1;
                this.cardTitle = "Create ";
            } else {
                this.inputQuestion = this.data;
                this.cardTitle = "Edit ";
            }
        },
        methods: {
            submit(event){
                this.inputQuestion[0].attempts = parseInt(this.inputQuestion[0].attempts);
                this.$emit('submitted', this.inputQuestion[0]);
 
                event.preventDefault();
            }
        }
    }
</script>

<style scoped>
    .true-false-inputs{
        max-width: 700px;
        background-color: deepskyblue;
        padding: 10px;
    }
</style>