<template>
  <div class="container">
    <h1>SpotiTime</h1>
    <div class="circle">
      <p>{{MsecondsTominuts(TotalPlayingTime)}} 分</p>
    </div>
    <div class="row">
      <button @click="spotifyLogin" class="button b-small">認証</button>
      <button @click="getHistory" class="button b-small">見る</button>
    </div>
    <!--
    <ul v-for="(value, index) in History">
      <li>{{ConvertJST(value.played_at)}}</li>
      <li>{{value.track.artists[0].name}} / {{value.track.name}}</li>
    </ul>
    -->
  </div>
</template>

<script>
import axios from 'axios'
// import moment from 'moment'
import * as moment from 'moment-timezone'
moment.tz.setDefault('Asia/Tokyo')

export default {
  data: function() {
    return {
      History: [],
      TotalPlayingTime: 0,
    }
  },
  props: {
    routeParams: Object
  },
  created: function() {
    if (this.$route.hash) {
      this.$router.push(this.$route.fullPath.replace('#', '?'))
    }
  },
  methods: {
    spotifyLogin: function() {
      let endpoint = 'https://accounts.spotify.com/authorize'
      let response_type = 'token'
      let client_id = 'bf090ae6942646348366507a7d6959bc'
      let redirect_uri = 'http://0.0.0.0:9000'
      let scope = 'user-read-recently-played'
      location.href = endpoint +
        '?response_type=' + response_type +
        '&client_id=' + client_id +
        '&redirect_uri=' + redirect_uri +
        '&scope=' + scope
    },
    getHistory: function() {
      let vm = this
      let endpoint = 'https://api.spotify.com/v1/me/player/recently-played'
      let data = {
        headers: {
          'Authorization': this.routeParams.token_type + ' ' + this.routeParams.access_token
        },
        data: {}
      }
      axios
      .get(endpoint, data)
      .then(res => {
        this.History = res.data.items
        this.getPlayingTime()
      })
      .catch(err => {
        console.error(err)
      })
    },
    DateDecision:function(date){
      const dateFrom = date.split('T')[0]
      const dateTo = new Date(Date.now()).toISOString().split('T')[0]
      if(dateFrom === dateTo){
        return true
      }
      else{
        return false
      }
    }, 
    getPlayingTime: function(){
      console.log("test")
      this.TotalPlayingTime = 0
      for (var i = 0, len = this.History.length; i < len; i++) {
        const playing_time = this.History[i].track.duration_ms
        const playing_date = this.History[i].played_at
        if(this.DateDecision(playing_date)){
          this.TotalPlayingTime += playing_time
        }
      }
    },
    ConvertJST: function(date){
      return moment(date).tz("Asia/Tokyo").format()
    },
    MsecondsTominuts(value){
      const result = Math.round((value / 1000 / 60) * 10) / 10
      return result
    },
    returnTime: function(){
      this.getHistory()
      this.getPlayingTime()
    }
  }
}
</script>

<style coped>
/*
.button {
  border: none; 
  background-color: rgb(228, 228, 228);
  border-radius: 30px;
}
*/

.button {
  width: 140px;
  height: 45px;
  font-family: 'Roboto', sans-serif;
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 2.5px;
  font-weight: 500;
  color: rgb(255, 255, 255);
  background-color: #191414;
  border: none;
  border-radius: 45px;
  box-shadow: 0px 8px 15px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease 0s;
  cursor: pointer;
  outline: none;
  }

.button:hover {
  background-color: #1DB954;
  box-shadow: 0px 15px 20px rgba(46, 229, 157, 0.4);
  color: #fff;
  transform: translateY(-2px);
}

.b-small{
  width: 100px;
  height: 40px;
  margin: 10px;
}

.b-middle{
  width: 60px;
  height: 40px;
}

.circle {
    margin:0 auto;
    width: 140px;
    height: 140px; 
    background-color: #a5a5a5;
    border-radius: 50%;/* ←円を作る */
    line-height: 140px;
    color: rgb(255, 255, 255);
}

.row{
  margin: 0 auto;
  padding: 30px 20px;
}
</style>

