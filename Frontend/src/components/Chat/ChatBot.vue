<template>
  <v-app>
    <v-content>
        <v-layout justify-center align-center>
          <v-flex text-xs-center>
              <div class="query">
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

                                <div v-for="r in a.result.fulfillment.messages"></div>
                            </td>
                        </tr>
                    </table>                    
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
    height: 50px;
    width: 100%;
    background-color:#ffffff;
    margin: auto;
}
.queryform{
  margin-top: 10px;   
  font-size: 20px;
  text-align: center;
}
.headline{
  font-weight: bold;
  text-align: center;
  margin-top: 200px;

}
.chat-window{
    width: 100%;
}
.bubble{
    width: auto;
    background-color: #ffffff;
    margin-right: 350px;
    padding: 16px;
    border-radius: 8px;
    color: #000000;
    float: right;
    font-size: 25px;
    animation: msg .25s linear;
}
.bubble-bot{
    width: auto;
    margin-left: 350px;
    background-color: #0066ff;
    padding: 16px;
    border-radius: 8px;
    color: #ffffff;
    float: left;
    font-size: 25px;
    animation: msg .25s linear;
}
@keyframes msg {
    0% {opacity: 0; transform: scale(0.8);} 
    100% {opacity: 1; transform: scale(1);}
}
</style>