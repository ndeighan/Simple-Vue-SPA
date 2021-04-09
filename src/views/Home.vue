<template>
    <div>
        <div class="tabs">
            <ul>
                <li ref="home" @click="changeDisplay('Home')" class="is-active"><a>Home</a></li>
                <li ref="create" @click="changeDisplay('Create')"><a>Create</a></li>
                <li ref="preview" @click="changeDisplay('Preview')"><a>Preview</a></li>
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
        <Create v-if="this.display === 'Create'" :questionData="QuestionData" />
        <Preview v-if="this.display === 'Preview'" :questionData="QuestionData" />
    </div>
</template>

<script>
    import Create from './Create.vue'
    import Preview from './Preview.vue'
    import axios from 'axios'
    import "@/assets/bulma.css"
    
    export default {
        name: 'home',
        components: {
            Create,
            Preview
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
                    //console.log("this.QuestionData", this.QuestionData)
                })
                .catch(err => {
                    console.log("err", err)
                })
            ])
             
        },
        methods: {
            changeDisplay(e) {
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