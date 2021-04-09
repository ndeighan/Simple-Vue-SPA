<template>
    <div class="input-question">
        <div class="question-content">
            <p class="is-size-5 has-text-weight-bold" v-bind:class="className">{{ question.questionText }}</p>
            <form ref="form" class="input-question-form">
                <input class="input" 
                       name="userInput" 
                       v-model="userInput"
                       :disabled="disableSubmit">
                <a href="#" 
                   v-on:click="submit" 
                   class="button"
                   :disabled="disableSubmit">
                    SUBMIT
                </a>
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
                userInput: 0,
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
                if(!isNaN(this.question.answer)){
                    this.userInput = parseInt(this.userInput);
                }

                if(this.userInput === this.question.answer){
                    this.question.isCorrect = true;
                    this.className = "is-success"; 
                    this.disableSubmit = true;
                } else {
                    this.className = "is-danger"; 
                }
            }
        }
    }
</script>
    
<style scoped>
    input{
        max-width: 100px;
        margin-right: 10px;
    } 
    .input-question-form {
        display: inline-block;
    }
    .is-success {
        background-color: #08CA7C;
    }
    .is-danger {
        background-color: #FF0020;
    }
</style>