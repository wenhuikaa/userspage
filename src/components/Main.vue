<template>
  <div>
    <h2 v-if="firstView">输入用户名搜索</h2>
    <h2 v-if="loading">loading</h2>
    <h2 v-if="errorMsg">{{errorMsg}}</h2>
    <div class="row">
      <div class="card" v-for="(user,index) in users" :key="index">
        <a :href="user.url" target="_blank">
          <img :src="user.avatar_url" style="width: 100px">
        </a>
        <p class="card-text">{{user.name}}</p>
      </div>
    </div>
  </div>
</template>

<script>
import PubSub from "pubsub-js";
import axios from "axios";
export default {
  data() {
    return {
      firstView: true,
      loading: false,
      users: null, //[{url:'',avatar_url:'',name:''}]
      errorMsg: ""
    };
  },

  mounted() {
    //不是在这里发送ajax请求，而是在点击search之后
    //订阅搜索的消息
    PubSub.subscribe("search", (msg, searchName) => {
      //在这里发送ajax请求
      const url = `https://api.github.com/search/users?q=${searchName}`;

      //更新状态（请求中）
      this.firstView = false;
      this.loading = true;
      //发ajax请求
      axios
        .get(url)
        .then(response => {
          //   console.log(response.data); // 得到返回结果数据
          const result = response.data;
          const users = result.items.map(item => ({
            //map将数组的值转为标签的值
            url: item.html_url,
            avatar_url: item.avatar_url,
            name: item.login
          }));
          //成功，则更新状态（成功）
          this.loading = false;
          this.users = users;
        })
        .catch(error => {
          //更新失败的状态
          this.loading = false;
          this.errorMsg = "请求失败";
        });
    });
  }
};
</script>

<style>
.card {
  float: left;
  width: 33.333%;
  padding: 0.75rem;
  margin-bottom: 2rem;
  border: 1px solid #efefef;
  text-align: center;
}

.card > img {
  margin-bottom: 0.75rem;
  border-radius: 100px;
}

.card-text {
  font-size: 85%;
}
</style>