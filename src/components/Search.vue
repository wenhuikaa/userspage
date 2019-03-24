<template>
  <section class="jumbotron">
    <h3 class="jumbotron-heading">Search Github Users</h3>
    <div>
      <input type="text" placeholder="enter the name you search" v-model="searchName">
      <button @click="search">Search</button>
    </div>
  </section>
</template>

<script>
//子组件之间的通信
import PubSub from 'pubsub-js'
export default {
    data(){
        return{
            searchName:''
        }
    },

    methods:{
        //在search.vue中发布消息，在main.vue里订阅消息
        search(){
            const searchName=this.searchName.trim()
            if(searchName){
                //发布搜索的消息'search',传过去一个searchName
                PubSub.publish('search',searchName)
            }
        }
    }
};
</script>

<style>
</style>