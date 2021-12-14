<template>
  <div class="app">
    <!-- 会計履歴を表示するスペース（ボタンを押すと表示） -->
    <div class="subTab" id="subTab" v-bind:style="{display:nowStyle}">
      <button class="closeButton">×</button>
      <div class="subTitleSpace">
        <h2 class="subTitle">会計履歴</h2>
      </div>
      <div class="subContents">
        <table>
          <thead>
            <tr>
              <th>時間帯</th>
              <th>金額</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="history in total_history" :key="history.id">
              <td>{{history}}</td>
              <td>{{history}}円</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- 会計処理を行うスペース（常時表示） -->
    <div class="mainTab" id="mainTab">
      <div class="titleBar">
        <h1 class="titleText">Smart-Cashier</h1>
        <button @click="openHistory">会計履歴</button>
      </div>
      <div class="previewScreen">
        <div class="previewContents">
          <h2>合計金額：{{totalPrice}}円</h2>
          <h3>お預かり：<input class="outOfForm" v-model="outOf">円</h3>
          <h3>お釣り：<p class="changeText">{{change}}円</p></h3>
        </div>
      </div>
      <button class="cashierButton" @click="cashier">確定</button>

      <div class="menuContents">
        <div class="itemList">
          <table>
            <thead>
              <tr>
                <th>商品名</th>
                <th>価格</th>
                <th>備考</th>
                <th>個数</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in item_list" :key="item.id">
                <td>{{item.name}}</td>
                <td>{{item.price}}円</td>
                <td>{{item.remarks}}</td>
                <td>{{item.amount}} 個 <button @click="reduceItem(item)">-</button><button @click="addItem(item)">+</button></td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {ref} from 'vue'

export default {
  name: 'App',
  setup(){
    let totalPrice=ref(0)
    let outOf=ref(0)
    let change=ref(0)
    let amount=ref(0)
    let nowStyle = ref("none")
    let dateObj = new Date()
    let now_date = ref(dateObj.getFullYear() + '年' + 
       ('00' + (dateObj.getMonth() + 1)).slice(-2) + '月' + 
       ('00' + dateObj.getDate()).slice(-2) + '日' + 
       ('00' + dateObj.getHours()).slice(-2) + '時' + 
       ('00' + dateObj.getMinutes()).slice(-2) + '分');

    let item_list=ref([
      {name:"やきそば(塩)",price:600,remarks:"",amount:0},
      {name:"やきそば(ソース)",price:650,remarks:"！オイスターソースには大豆が含まれています！",amount:0}
    ])

    let displayStyle = ref([
      {D_style:"none"},
      {D_style:"block"}
    ])

    let total_history = ref([
      {"2020/12/13" : "1500"},
    ])

    let subTabCtrl = ref(document.getElementById("subTab"))

    const addItem=(i)=>{
      totalPrice.value = totalPrice.value + i.price;  
      i.amount++
    }

    const reduceItem=(i)=>{
      totalPrice.value = totalPrice.value - i.price;
      i.amount--
    }

    const cashier=()=>{
      change.value =  outOf.value - totalPrice.value;
      total_history.value.date = now_date.value;
      total_history.value.earning = totalPrice.value;
      console.log(now_date.value)     
    }

    const openHistory=()=>{
      if(nowStyle.value == "block"){
        nowStyle.value="none";
        console.log("非表示")
      }else{
        nowStyle.value="block";
        console.log("表示")
      }
    }


    return{
      totalPrice,
      outOf,
      change,
      item_list,
      total_history,
      amount,
      now_date,
      subTabCtrl,
      displayStyle,
      nowStyle,
      addItem,
      reduceItem,
      cashier,
      openHistory,
    }
  }

}
</script>

<style>
*{
  margin:0;
}

.subTab{
  background-color:rgb(247, 189, 82);
  position:fixed;
  top:0;
  right:0px;
  width:400px;
}

.subContents{
  border:solid white;
  border-radius:10px;
  margin:10px;
}

.titleBar{
  padding-left:10px;
  background-color:rgb(255, 200, 98)
}

.titleText{
  color:white;
}

.cashierButton{
  display:inline-block;

}

.historyButtonButton{
  display:inline;
}

.previewScreen{
  border:solid;
  border-radius:10px;
  width:300px;
  margin-right:10px;
  margin-left:10px;
  margin-top:10px;
  margin-bottom:10px;
  background-color:white;
  display:inline-block;
}

.previewContents{
  padding-left:5px;
  padding-top:5px;
  padding-bottom:5px;
  display:inline-block;
}

.menuContents{
  border:solid;
  border-radius:10px;
  margin-left:10px;
  margin-top:40px;
}

.itemAmount{
  width:20px;
}

table{
  margin:0 auto;
}

.itemList{
  padding:0.5em;
}

td{
  padding:0.5em;
  border:1px solid rgba(247, 173, 63, 0.905);
}

button{
  padding:5px,10px;
  margin-left:5px;
}

.outOfForm{
  width:130px;
  font-size:30px;
  text-align:right;
}

.changeText{
  display:inline;
  color:red;
}

.cashierButton{
  position:fixed;
  margin-top:10px;
  left:320px;
  background-color:rgba(255, 175, 27, 0.576);
  border:solid;
  border-radius:10px;
  font-size:30px;
}

</style>
