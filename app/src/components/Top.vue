<template>
  <div class="container">
    <h1>Topページ</h1>
    <button @click="spotifyLogin">認証</button>
    <button @click="getHistory">取得テスト用</button>
    <button @click="getPlayingTime">表示テスト用</button>
    <p>合計再生時間: {{TotalPlayingTime}}</p>
    <ul v-for="(value, index) in History">
      <li>{{ConvertJST(value.played_at)}}</li>
      <li>{{DateDecision(value.played_at)}}</li>
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
      History: null,
      TotalPlayingTime: null
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
      })
      .catch(err => {
        console.error(err)
      })
    },
    getPlayingTime: function(){
      console.log(this.History.length)
      for (var i = 0, len = this.History.length; i < len; i++) {
          console.log(this.History[i].track.duration_ms)
          this.TotalPlayingTime += this.History[i].track.duration_ms
      }
    },
    ConvertJST: function(date){
      return moment(date).tz("Asia/Tokyo").format()
    },
    DateDecision:function(date){
      const dateFrom = date.split('T')[0]
      console.log(dateFrom)
      const dateTo = new Date(Date.now()).toISOString().split('T')[0]
      if(dateFrom === dateTo){
        return true
      }
      else{
        return false
      }
    }
    
  }
}
</script>
