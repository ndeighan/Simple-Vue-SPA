<template>
    <div>
        <div class="tabs">
            <ul>
                <li ref="home" @click="changeDisplay('Home')" class="is-active"><a>Home</a></li>
                <li ref="quizzes" @click="changeDisplay('Quizzes')"><a>Quizzes</a></li>
<!--                <li ref="library" @click="changeDisplay('Library')"><a>Library</a></li>-->
            </ul>
        </div>
        <div class="home" v-if="this.display === 'Home'">
            <header class="hero is-primary">
                <div class="hero-body">
                    <p class="title">
                        Home
                    </p>
                </div>
            </header>
            
        </div>
        <Quizzes v-if="this.display === 'Quizzes'" :quizzesData="QuestionData" />
        <Preview v-if="this.display === 'Preview'" :questionData="QuestionData" />
<!--        <Library v-if="this.display === 'Library'" :quizzesData="QuestionData" />-->
    </div>
</template>

<script>
    import Preview from './Preview.vue'
    import Quizzes from './Quizzes.vue'
//    import Library from './Library.vue'
    import axios from 'axios'
    import "@/assets/bulma.css"
    
    export default {
        name: 'home',
        components: {
            Preview,
            Quizzes
            //Library
        },
        data() {
            return ({
                display: "Home",
                QuestionData: []
            })
        },
         mounted(){
            Promise.all([ 
                axios
                .get('http://localhost:2020')
                .then(response => (this.info = response))
                .then((json) => {
                    this.QuestionData = json.data;
                    console.log("this.QuestionData", this.QuestionData)
                })
                .catch(err => {
                    console.log("err", err)
                })
            ])
             
        },
        methods: {
            changeDisplay(e) {
                console.log("changeDisplay", e)
                this.display = e;
                
                Object.keys(this.$refs).forEach(key => {
                    const value = this.$refs[key];
                  
                    if(value.innerText === this.display){
                        value.classList.add("is-active");
                    } else {
                        value.classList.remove("is-active");
                    }
                });
            }
        }
    }
</script>

<style scoped>
</style>