<template>
  <div class="container">
    <h1>Topページ</h1>
    <button @click="spotifyLogin">認証</button>
    <button @click="getHistory">取得テスト用</button>
    <ul v-for="(value, index) in History" key="index">
      <li>{{value.played_at}}</li>
    </ul>

  </div>
</template>

<script>
import axios from 'axios'

export default {
  data: function() {
    return {
      History: null
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
    }
  }
}
</script>
