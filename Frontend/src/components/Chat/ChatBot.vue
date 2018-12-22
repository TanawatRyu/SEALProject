<template>
  <v-app>
    <v-content>
        <v-layout justify-center align-center>
          <v-flex text-xs-center>
              <div class="query">
                <input :aria-label="config.locale.strings.queryTitle" autocomplete="off" v-model="query" class="queryform" @keyup.enter="submit()" :placeholder="config.locale.strings.queryTitle" autofocus type="text">
              </div>
                <div v-if="answers.length == 0">
                    <div class="headline">
                        <div class="material-icons up">arrow_upward</div>
                        <br>
                        <br>
                            {{config.locale.strings.welcomeTitle}}

                        <p class="body2">{{config.locale.strings.welcomeDescription}}</p>
                    </div>
                </div>    
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
        speech: config.locale.strings.voiceTitle,
        micro: false,
        muted: config.app.muted,
        online: navigator.onLine,
        config
        }
    },
    methods: {
        submit(){

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
    background-color:white;
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
.material-icons{

}
</style>