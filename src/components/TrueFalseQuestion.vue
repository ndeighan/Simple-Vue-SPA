<template>
    <div class="input-question">
        <div class="question-content">
            <p class="is-size-5 has-text-weight-bold" v-bind:class="className">{{ question.questionText }}</p>
            <form ref="form" class="true-false-question">
                <label for="true" class="radio">           
                    <input type="radio" 
                           id="true" 
                           value=true 
                           v-model="userInput"
                           :disabled="disableSubmit">
                    True
                </label>
                <br>
                <label for="false" class="radio">
                    <input type="radio" 
                           id="false" 
                           value=false 
                           v-model="userInput"
                           :disabled="disableSubmit">
                    False
                </label>
                <br>
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
                userInput: null,
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
                if(this.userInput === this.question.answer){
                    this.className = "is-success"; 
                    this.question.isCorrect = true;
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
    } 
    .is-success {
        background-color: #08CA7C;
    }
    .is-danger {
        background-color: #FF0020;
    }
    .true-false-question {
        display: inline-grid;
    }
</style>