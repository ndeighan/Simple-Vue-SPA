<template>
    <div class="multiple-choice-question">
        <div class="question-content">
            <p class="is-size-5 has-text-weight-bold" v-bind:class="className">{{ question.questionText }}</p>
            <form ref="form" class="multiple-choice-question-form">
                <div v-if="question.multipleAnswer === 'true'">
                    multipleAnswer:
                    <label v-for="option in question.options" 
                           class="checkbox" :key="option.optionId">           
                        <input type="checkbox" 
                               :id=option.optionId 
                               :value=option.optionId
                               v-model="userInput"
                               :disabled="disableSubmit">
                        {{option.optionText}}
                    </label>
                    <br>

                    <a href="#" v-on:click="submit" class="button">SUBMIT</a>
                </div>
                <div v-else>
                    singleAnswer:
                    <label v-for="option in question.options" 
                           class="radio" :key="option.optionId">           
                        <input type="radio" 
                               :id=option.optionId 
                               :value=option.optionId
                               v-model="userInput"
                               :disabled="disableSubmit">
                        {{option.optionText}}
                    </label>
                    <br>

                    <a href="#" 
                       v-on:click="submit" 
                       class="button"
                       :disabled="disableSubmit">
                        SUBMIT
                    </a>
                </div>
            </form>
        </div>
    </div>
</template>
<script>
    export default {
        props: [
            'question'
        ],
        data(){
            return({
                userInput: [],
                className: "",
                attempts: 0,
                disableSubmit: false
            })
        },
        methods: {
            submit(event){
                if(this.question.attempts === 0){
                    this.processSubmission();
                } else if(this.attempts < this.question.attempts){
                    this.attempts++;
                    this.processSubmission();
                    if(this.attempts === this.question.attempts){
                        this.disableSubmit = true;
                    }
                }
                this.$emit('submitted', this.question.questionId);
                event.preventDefault();
            },
            processSubmission(){
                let options = this.question.options;
                let correctAnswers = [];

                if(this.question.multipleAnswer === "true"){
                    for(var i=0; i<options.length; i++){
                        if(options[i].correct === "true"){
                            correctAnswers.push(options[i].optionId)
                        }
                    }
                    this.question.isCorrect = this.compareMultipleAnswers(correctAnswers, this.userInput);
                } else {
                    
                    for(i=0; i<options.length; i++){
                        if(this.userInput === options[i].optionId){
                            this.question.isCorrect = options[i].correct;
                        }
                    } 
                }

                if(this.question.isCorrect === "true"){
                    this.className = "is-success";
                    this.disableSubmit = true;
                } else {
                    this.className = "is-danger"; 
                }
            },
            compareMultipleAnswers(a, b){
                return Array.isArray(a) &&
                Array.isArray(b) &&
                a.length === b.length &&
                a.every((val, index) => val === b[index]);
            }
        }
    }
</script>
<style scoped>
    .multiple-choice-question-form {
        display: inline-grid;
    }
    .is-success {
        background-color: #08CA7C;
    }
    .is-danger {
        background-color: #FF0020;
    }
</style>