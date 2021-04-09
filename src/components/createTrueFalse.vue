<template>
    <div class="true-false-inputs" v-bind="trueFalseQuestion">
        <div>
            {{cardTitle}}True/False
        </div>
        <hr />
        <div><p><strong>Question ID: </strong>{{trueFalseQuestion[0].questionId}}</p></div>
        <hr />
        <div><p><strong>Question Type: </strong>{{trueFalseQuestion[0].questionType}}</p></div>
        <hr />
        <div class="new-question-text">
            <p class="is-size-6 has-text-weight-bold">Question text:</p><br>
            <textarea type="text" name="body" v-model="trueFalseQuestion[0].questionText" rows="4" cols="50" placeholder="Insert question." />
        </div>
        <hr />
        <div>
            <p class="is-size-6 has-text-weight-bold">Answer: </p>
            <form ref="form" class="true-false-question">
                <label for="true" class="radio">           
                    <input type="radio" 
                           id="true" 
                           value=true 
                           v-model="trueFalseQuestion[0].answer">
                    True
                </label>
                <br>
                <label for="false" class="radio">
                    <input type="radio" 
                           id="false" 
                           value=false 
                           v-model="trueFalseQuestion[0].answer">
                    False
                </label>
                <br>
            </form>
        </div>
        <hr />
        <div>
            <p class="is-size-6 has-text-weight-bold">Attempts:</p>
            <input type="number" name="attempts" size="50" 
                   v-model="trueFalseQuestion[0].attempts">
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
        name: 'CreateTrueFalse',
        props: ['data'],
        data(){
            return {
                trueFalseQuestion: [{
                    "questionId": 0,
                    "questionType": "trueFalse", //Question type
                    "questionText": "", //Question text
                    "answer": "true", //"true" or "false"
                    "isCorrect": false,
                    "attempts": 0 //attempts, 0 = infinite
                }],
                cardTitle: ""
            }
        },
        mounted() {
            if(this.data.questions){
                this.trueFalseQuestion[0].questionId = this.data.questions.length+1;
                this.cardTitle = "Create ";
            } else {
                this.trueFalseQuestion = this.data;
                this.cardTitle = "Edit ";
            }
            
            
        },
        methods: {
            submit(event){
                this.trueFalseQuestion[0].attempts = parseInt(this.trueFalseQuestion[0].attempts);
                this.$emit('submitted', this.trueFalseQuestion[0]);
                
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