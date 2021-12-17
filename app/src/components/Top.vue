<template>
  <div class="container">
    <h1>SpotiTime</h1>
    <button @click="spotifyLogin">認証</button>
    <button @click="getHistory">取得テスト用</button>
    <p>合計再生時間: {{MsecondsTominuts(TotalPlayingTime)}}</p>
    <ul v-for="(value, index) in History">
      <li>{{ConvertJST(value.played_at)}}</li>
      <li>{{value.track.duration_ms}}</li>
    </ul>
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
      TotalPlayingTime: 0
    }
  },
  props: {
    routeParams: Object
  },
  computed: {
      get_sum_playtime: function (History) {
        console.log(History)
        return History
      }
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
