<template>
  <v-app>
    <v-content>
        <v-layout justify-center align-center>
          <v-flex text-xs-center>
              <div class="query">
                <i class="material-icons chat">chat</i>  
                <input :aria-label="config.locale.strings.queryTitle" autocomplete="off" v-model="query" class="queryform" @keyup.enter="submit()" :placeholder="config.locale.strings.queryTitle" autofocus type="text">
              </div>
              <section class="wrapper">
                    <div v-if="answers.length == 0">
                        <div class="headline">
                            <div class="material-icons up">arrow_upward</div>
                            <br>
                            <br>
                                {{config.locale.strings.welcomeTitle}}

                            <p class="body2">{{config.locale.strings.welcomeDescription}}</p>
                        </div>
                    </div>
                    <br>
                    <table v-for="a in answers" class="chat-window">

                        <tr>
                            <td class="bubble">{{a.result.resolvedQuery}}</td>
                        </tr>
                        <tr>
                            <td>
                                <div v-if="a.result.fulfillment.speech" class="bubble-bot">
                                        {{a.result.fulfillment.speech}}
                                </div>
                            </td>
                        </tr>
                        <tr>
                            <td>         
                                <div v-for="r in a.result.fulfillment.messages">
                                    <div v-if="r.unknown == true" class="google-chip">
                                        <a class="suggestion" :href="'https://www.google.com/search?q=' + r.text" target="_blank">
                                        Search for "{{r.text}}" on Google <i class="material-icons openlink">search</i>
                                        </a>
                                    </div>
                                </div>
                            </td>
                        </tr>
                    </table> 
                    <a id="bottom"></a>                   
                </section>    
          </v-flex>
        </v-layout>
    </v-content>
  </v-app>
</template>

<script>
import { ApiAiClient } from 'api-ai-javascript'
import config from './config'

const client = new ApiAiClient({accessToken: config.app.token})

export default {
    name:'ChatBot',
    data: function(){
    return {
        answers: [],
        query: '',
        online: navigator.onLine,
        config
        }
    },
    watch: {
        answers: function(val){
            setTimeout(() => { 
                document.querySelector('#bottom').scrollIntoView({ 
                    behavior: 'smooth' 
                })
            }, 2) // if new answers arrive, wait for render and then smoothly scroll down to #bottom selector, used as anchor
        }
    },
    methods: {
        submit(){
            client.textRequest(this.query).then((response) => {
                if(response.result.action == "input.unknown" && this.config.app.googleIt == true){
                    response.result.fulfillment.messages[0].unknown = true
                    response.result.fulfillment.messages[0].text = response.result.resolvedQuery
                } // if the googleIt is enabled, show the button

                this.answers.push(response)

                this.query = ''
            })            
        }
    }

}
</script>
<style>
#app {
    background-color: #e0e0e0;
}
.query{
    height: 80px;
    width: 100%;
    background-color:#ffffff;
    margin: auto;
    position: fixed;
}
.queryform{  
    font-size: 30px;
    width: 30%;
    text-align: center;
    margin-bottom: 50px;
}
.headline{
    font-weight: bold;
    text-align: center;
    margin-top: 200px;

}
.wrapper{
    margin-top:100px;
    max-width: 1000px;
    margin-right: auto;
    margin-left: auto;
}
.chat-window{
    width:100%;
}
.bubble{
    max-width: 600px;
    height: auto;
    background-color: #ffffff;
    margin-top: 15px;
    padding: 15px;
    border-radius: 8px;
    color: #000000;
    float: right;
    font-size: 25px;
    animation: msg .25s linear;
    word-wrap: break-word;
    text-align: left;  
}
.bubble-bot{
    max-width: 600px;
    height: auto;
    margin-top: 15px;
    background-color: #0066ff;
    padding: 15px;
    border-radius: 8px;
    color: #ffffff;
    float: left;
    font-size: 25px;
    word-wrap: break-word;
    animation: msg .25s linear;
    text-align: left;  
}
@keyframes msg {
    0% {opacity: 0; transform: scale(0.8);} 
    100% {opacity: 1; transform: scale(1);}
}
.google-chip{
    background-color: #3d8bff;
    margin-top: 15px;
    padding: 15px;
    float: left;
    font-size: 25px;
    animation: msg .25s linear;
    border-radius: 50px; 
    max-width: 600px;
    word-wrap: break-word;
    text-align: left;  
}
.chat{
    margin-top: 15px;
    margin-right: 20px;
    font-size: 50px;  
}
.suggestion,a:hover{
    color: #ffffff;
}
</style>