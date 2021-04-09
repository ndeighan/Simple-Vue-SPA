<template>
    <div>
        <div class="hero is-primary">
            <div class="hero-body">
                <p class="title">Create</p>
            </div>
        </div>
        <div class="container">
            <div class="create-container">
            <div class="select-question container">
                <p class="is-size-6 has-text-weight-bold">Create</p>
                <p>Chose component:</p>
                <select class="comments-select-user" v-model="selected">
                  <option disabled value="">Please select a question type</option>
                  <option v-for="questionType in questionTypes" :key="questionType.id">{{questionType.type}}</option>
                </select>
                <button class="button" @click="getQuestion()">Add</button>
            </div>
            <div class="question-inputs container">
                <CreateTrueFalse v-if="this.display === 'trueFalse'" 
                         :data="this.questionData" 
                         @submitted="questionSubmitted" />

                <CreateMultipleChoice v-if="this.display === 'mcq'" 
                                      :data="this.questionData" 
                                      @submitted="questionSubmitted" />

                <CreateInput v-if="this.display === 'input'" 
                             :data="this.questionData" 
                             @submitted="questionSubmitted"/>
            </div>
        </div>
        </div>
        
    </div>
</template>

<script>
    import CreateTrueFalse from '../components/createTrueFalse';
    import CreateMultipleChoice from '../components/createMultipleChoice';
    import CreateInput from '../components/createInput';
    
    import Vue from 'vue'
    import axios from 'axios';
    import Toasted from 'vue-toasted';
    
    export default {
        name: 'create',
        components: {
            CreateTrueFalse,
            CreateMultipleChoice,
            CreateInput
        },
        props: ['questionData'],
        data(){
            return ({
                questionTypes: [
                    {
                        id: 1,
                        type: "trueFalse"
                    },
                    {
                        id: 2,
                        type: "input"
                    },
                    {
                        id: 3,
                        type: "mcq"
                    }
                ],
                selected: [],
                display: ""
            })
        },
        mounted(){
            Vue.use(Toasted);
        },
        methods: {
            getQuestion(){
                this.display = this.selected;
            },
            questionSubmitted(question){
                this.questionData.questions.push(question); 
                Promise.all([ 
                    axios
                    .post('http://localhost:2020', this.questionData)
                    .then((json) => {
                        if(json.status === 200){
                            this.display = "";
                            //Show addition confirmation
                            this.$toasted.show("Question added sucessfully.", { 
                                     theme: "outline", 
                                     position: "top-center", 
                                     duration : 2000
                                });
                        }
                    })
                    .catch(err => {
                        console.log("err", err)
                    })
                ])
            }
        }
    }
</script>
    
<style scoped>
    
</style>