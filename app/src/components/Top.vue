<template>
  <div class="container">
    <h1>Topページ</h1>
    <button @click="spotifyLogin">認証</button>
    <p>{{ routeParams }}</p>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      hoge: "test"
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
      let scope = 'user-read-currently-playing'
      location.href = endpoint +
        '?response_type=' + response_type +
        '&client_id=' + client_id +
        '&redirect_uri=' + redirect_uri +
        '&scope=' + scope
    }
  }
}
</script>
