<template>
  <div id="app">
    <!--<img src="./assets/logo.png">-->
    <!--<router-view/>-->
    <h1>{{title}}</h1>
    <div id="listContent">
      <input id="add-input" v-model="newItem" v-on:keyup.enter="addNew" placeholder="do what?">
      <ul class="lists" v-if="items.length>0">
        <li v-for="(item,index) in currentItems">
          <h3 v-on:mouseenter="itemEnter(item)" v-on:mouseleave="itemLeave(item)" >
            <input type="checkbox" v-bind:checked="item.isFinished" v-on:click="toggleFinished(item)">
            <p class="item-label" v-bind:class="{finished: item.isFinished}">
              {{index+1}}. {{item.label}}
            </p>
            <p class="item-status" v-if='item.isFinished'>finished</p>
            <p class="item-delete" v-if='item.showDelete' @click='deleteClick(index)'>Delete</p>
          </h3>
        </li>
      </ul>
      <ul class="listsOption" v-if="items.length>0">
        <li>
          <button v-on:click="btn = 'showAll'">全部{{items.length}}项</button>
          <button v-on:click="btn = 'showUnFinish'">未完成{{unfinishedItems.length}}项</button>
          <button v-on:click="btn = 'showFinish'">已完成{{finishedItems.length}}项</button>
        </li>
        <li><button v-on:click="clearFinish">清空已完成</button></li>
      </ul>
    </div>
  </div>
</template>

<script>
import Store from './store'
export default {
  name: 'App',
  data(){
    return {
      title: 'Just do It !',
      newItem: '',
      items: Store.fetch(),
      btn: ''
    }
  },
  methods: {
    toggleFinished(item){
      item.isFinished = !item.isFinished
    },
    addNew(){
      this.items.push({label: this.newItem, isFinished: false, showDelete: false})
      this.newItem = ''
    },
    itemEnter(item){
      item.showDelete = true
    },
    itemLeave(item){
      item.showDelete = false
    },
    deleteClick(index){
      this.items.splice(index,1)
    },
    clearFinish(){
      this.items = this.unfinishedItems
    }
  },
  watch: {
    items: {
      //监听items的变化并将改变存储到localStorage
      handler(items){
        Store.save(items)
      },
      deep: true
    }
  },
  computed: {
    currentItems(){
      switch (this.btn) {
        case 'showAll': return this.items;break;
        case 'showFinish': return this.finishedItems;break;
        case 'showUnFinish': return this.unfinishedItems;break;
        default: return this.items;break;
      }
    },
    finishedItems(){
      return this.items.filter((item ,index,array) => item.isFinished === true)
    },
    unfinishedItems(){
      return this.items.filter((item ,index,array) => item.isFinished === false)
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto 100px;
}
h1{font-size: 50px;}
#listContent {
  width: 600px;
  margin: 0 auto;
  border: 1px solid gray;
  border-radius: 10px;
  background: #fff;
  box-shadow: 0 0 10px 5px #aaa;
}
#add-input {
  width: 560px;
  line-height: 50px;
  border: none;
  font-size: 20px;
  padding-left: 40px;
  border-radius: 10px;
}
h3>input { transform: scale(1.5,1.5) }
ul {
  margin: 0 auto;
  list-style: none;
  text-align: left;
  border-top: 1px solid gray;
}
.item-label { display: inline;}
.item-status {
  display: inline;
  background: red;
  color: #fff;
  padding: 0 5px;
  margin: 0 5px;
  font-size: 14px;
}
.item-delete {
  display: inline;
  text-decoration: underline;
  font-size: 14px;
  color: gray;
  cursor: pointer;
}
.finished{
  text-decoration: line-through;
  color: gray;
}
.listsOption {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-right: 40px;
}
.listsOption li{
  display: inline-block;
  margin: 10px 0;
  font-size: 15px;
}
.listsOption li button {
  background: transparent;
  color: gray;
  border: none;
}
.listsOption li button:hover{
  color: darksalmon;
}
</style>
