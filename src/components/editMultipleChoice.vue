<template>
<div class="edit-mcq">
    <div>
            Edit MCQ
        </div>
        <hr />
        <div><p><strong>Question ID: </strong>{{mcqQuestion.questionId}}</p></div>
        <hr />
        <div><p><strong>Question Type: </strong>{{mcqQuestion.questionType}}</p></div>
        <hr />
        <div class="new-question-text">
            <p class="is-size-6 has-text-weight-bold">Question text:</p><br>
            <textarea type="text" name="body" v-model="mcqQuestion.questionText" rows="4" cols="50" placeholder="Insert question text." />
        </div>
        <hr />
        <div>
            <p class="is-size-6 has-text-weight-bold">Multiple Answer: </p>
            <form ref="form" class="mcq-question">
                <label for="true" class="radio">           
                    <input type="radio" 
                           id="true" 
                           value=true 
                           v-model="mcqQuestion.multipleAnswer">
                    Yes
                </label>
                <br>
                <label for="false" class="radio">
                    <input type="radio" 
                           id="false" 
                           value=false 
                           v-model="mcqQuestion.multipleAnswer">
                    No
                </label>
                <br>
            </form>
        </div>
        <hr />
        <div class="add-answer">
            <button class="add-answer-button button" @click="addAnswer">Add Answer</button>
            <hr />
            <div class="new-answer" v-for ="option in mcqQuestion.options" 
                 :key="option.optionId">
                <div><p><strong>Option Id: </strong>{{option.optionId}}</p></div>
                <div class="new-question-title">
                    <p class="option-title is-size-6 has-text-weight-bold">Option text:</p><br>
                    <div class="remove-answer">
                        <button class="remove-answer-button button" 
                                :id="option.optionId"
                                @click="removeAnswer">Remove Answer</button>
                    </div>
                </div>
                <textarea type="text" name="body" v-model="option.optionText" rows="4" cols="50" placeholder="Insert possible answer." />
                <div class="correct-options">
                    <p class="is-size-6 has-text-weight-bold">Correct?: </p>
                    <form ref="form" class="mcq-option">
                        <label for="true" class="radio">           
                            <input type="radio" 
                                   id="true" 
                                   value=true 
                                   v-model="option.correct">
                            Yes
                        </label>
                        <br>
                        <label for="false" class="radio">
                            <input type="radio" 
                                   id="false" 
                                   value=false 
                                   v-model="option.correct">
                            No
                        </label>
                        <br>
                    </form>
                </div> 
            </div>
            <hr />
            <div>
                <p>Submit question to quiz</p>
                <a href="#" @click="submit" class="button">
                    SUBMIT
                </a>
            </div>
        </div>
</div>
</template>
<script>
    export default {
        name: 'EditMCQ',
        props: ['quizzesData', 'quizId', 'questionId'],
        data(){
            return {
                mcqQuestion: {
                    "questionId": 0,
                    "questionType": "mcq",
                    "questionText": "",
                    "multipleAnswer": "false",
                    "options": [],
                    "isCorrect": false,
                    "attempts": 1
                },
                option: {
                    "optionId": 0,
                    "optionText": "",
                    "correct": "false"
                },
                questionToEdit: {}
            }
        },
        mounted() {
            this.mcqQuestion = this.quizzesData.quizzes[this.quizId-1].questions[this.questionId-1];
            console.log("mcqQuestion", this.mcqQuestion);
        },
        methods: {
            submit(event){
                console.log("edit mcqQuestion submit", this.mcqQuestion);
                this.mcqQuestion.attempts = parseInt(this.mcqQuestion.attempts);
                this.$emit('submitted', this.mcqQuestion, this.quizId);


                event.preventDefault();
            },
                        addAnswer(){ 
                //Generate optionId
                this.option.optionId = this.mcqQuestion.options.length + 1;
                //clone to new object
                var newOption = JSON.parse(JSON.stringify(this.option))
                //Add new option to question
                this.mcqQuestion.options.push(newOption);
                
            },
            removeAnswer(event){
                //Return options without the one selected
                const filteredOptions = this.mcqQuestion.options
                                            .filter(option => option.optionId != event.target.id);
                //clone to new object
                const newOptions = JSON.parse(JSON.stringify(filteredOptions))
                //Reset optionIds
                for(var i = 0; i < newOptions.length; i++){
                    newOptions[i].optionId = i+1;
                }
                //Copy amended options to question
                this.mcqQuestion.options = newOptions;
            }
        }
    }
</script>
<style scoped>

</style>