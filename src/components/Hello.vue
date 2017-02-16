<template>
  <div class="hello">
    <div v-if="!logined">
      <input v-model="account" placeholder="userid:password" @keyup.enter="login">
      <button @click="login">로그인</button>
    </div>
    <div v-if="logined" v-for="item in list">
      <div v-html="item.title[0]['_']"></div>
    </div>
</template>

<script>
  import { parseString } from 'xml2js'

  export default {
    name: 'hello',
    data() {
      return {
        list: [],
        logined: false,
        account: ''
      }
    },
    created() {
      if (!this.$http.defaults.headers.common['Authorization']) {
        return
      }

      this.getList()
    },
    methods: {
      getList() {
        let self = this
        this.$http.get('/api?maxResults=15&os_authType=basic&title=newsfeed')
          .then(function (res) {
            parseString(res.data, function (err, result) {
              self.list = result.feed.entry
              console.log(JSON.parse(JSON.stringify(result.feed.entry[0])))
            })
          })
          .catch(function (err) {
            alert('계정을 확인해주세요')
            self.logined = false
            self.account = ''
          })
      },
      login() {
        this.$http.defaults.headers.common['Authorization'] = 'Basic ' + btoa(this.account)
        this.logined = true
        this.getList()
      }
    }

  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1,
  h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }
</style>
