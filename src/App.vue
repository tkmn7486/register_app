<template>
  <div class="app">
    <!-- 会計履歴を表示するスペース（ボタンを押すと表示） -->
    <div class="subTab" id="subTab" v-bind:style="{display:nowSubTabStyle}">
      <button class="closeButton" @click="openHistory">×</button>
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
              <td>{{history.date}}</td>
              <td>{{history.price}}円</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

<!-- 会計前の最終確認画面 -->
    <div class="checkTab" id="checkTab" v-bind:style="{display:nowCheckTabStyle}">
      <div class="checkTabSpace">
        <div class="checkTabText">
          <h3 class="checkText1">以下で会計を確定します</h3>
          <div class="nowBill">
            <h3>購入総額：{{totalPrice}}円</h3>
            <h3>お預かり金額：{{outOf}}円</h3>
            <h3>お釣り：{{change}}円</h3>
          </div>
          <button class="YesButton" @click="checkButton">OK</button>
          <button class="NoButton" @click="returnButton">戻る</button>
        </div>
      </div>
    </div>

    <!-- 会計処理を行うスペース（常時表示） -->
    <div class="mainTab" id="mainTab">
      <div class="titleBar">
        <h1 class="titleText">Smart-Cashier</h1>
        <button class="historyButton" @click="openHistory">会計履歴(会計総数：{{cashierCount}}件)</button>
      </div>
      <div class="previewScreen">
        <div class="previewContents">
          <h2>合計金額：{{totalPrice}}円</h2>
          <h3>お預かり：<input class="outOfForm" v-model="outOf">円</h3>
          <!-- <h3>お釣り：<p class="changeText">{{change}}円</p></h3> -->
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
                <td>{{item.amount}} 個 <button class="reduceButton" @click="reduceItem(item)">−</button><button class="addButton" @click="addItem(item)">+</button></td>
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
    let cashierCount=ref(0)
    let nowSubTabStyle = ref("none")
    let nowCheckTabStyle =ref("none")
    // let boughtList = ref([])
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

    let cartItemList=ref([
    ])

    let displayStyle = ref([
      {D_style:"none"},
      {D_style:"block"}
    ])

    let total_history = ref([])

    let subTabCtrl = ref(document.getElementById("subTab"))

    const addItem=(i)=>{
      totalPrice.value = totalPrice.value + i.price;
      i.amount++
      cartItemList.value.push(i.name)
    }

    const reduceItem=(i)=>{
      totalPrice.value = totalPrice.value - i.price;
      i.amount--
      cartItemList.value.push(i.price)
    }

    const cashier=()=>{
      change.value =  outOf.value - totalPrice.value;
      nowCheckTabStyle.value = "block"
    }

    const openHistory=()=>{
      if(nowSubTabStyle.value == "block"){
        nowSubTabStyle.value="none";
        console.log("非表示")
      }else{
        nowSubTabStyle.value="block";
        console.log("表示")
      }
    }

// 会計処理
    const checkButton=()=>{
      total_history.value.push({date: now_date.value, price: totalPrice.value})
      console.log(now_date.value)
      totalPrice.value = 0
      outOf.value = 0
      change.value = 0
      cashierCount.value++

      nowCheckTabStyle.value = "none"
    }

    const returnButton=()=>{
      nowCheckTabStyle.value = "none"
    }

    // const getBoughtItems=()=>{
    //   for(let co = 0; co < cartItemList.value.length; co++){
    //     boughtList.value.push(cartItemList.value.name[co])
    //   }
    // }


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
      nowSubTabStyle,
      nowCheckTabStyle,
      cartItemList,
      cashierCount,
      // boughtList,
      addItem,
      reduceItem,
      cashier,
      openHistory,
      // getBoughtItems,
      checkButton,
      returnButton,
    }
  }

}
</script>

<style>
*{
  margin:0;
}

html{
  background-color:rgb(250, 224, 177)
}

.subTab{
  background-color:rgb(247, 189, 82);
  position:fixed;
  z-index:5px;
  top:0;
  right:0px;
  width:400px;
}

.subContents{
  border:solid white;
  border-radius:10px;
  margin:10px;
}

.checkTab{
  position:fixed;
  inset:0;
  margin:auto;
  background-color:rgba(15, 15, 15, 0.692);
}

.checkTabSpace{
  margin:auto;
  margin-top:100px;
  border:solid;
  border-radius:10px;
  background-color:white;
  width:500px;
  height:300px;
}

.checkTabText{
  margin-top:50px;
  text-align:center;
}

.nowBill{
  padding-top:20px;
  padding-bottom:30px;
}

.YesButton{
  border:solid;
  border-radius:10px;
  border-color:rgb(252, 76, 22);
  width:60px;
  height:30px;
  background-color:rgb(255, 133, 111);
}

.NoButton{
  border:solid;
  border-radius:10px;
  border-color:rgb(22, 252, 210);
  width:60px;
  height:30px;
  background-color:rgb(111, 255, 243);
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

.historyButton{
  border:solid white;
  border-radius:10px;
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
  background-color:white;
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
  margin-top:10px;
  left:320px;
  background-color:rgba(255, 175, 27, 0.576);
  border:solid;
  border-radius:10px;
  font-size:30px;
}

.reduceButton{
  border:solid #6ea5f8;
  border-radius:30px;
  background-color:#8dbafd;
  width:30px;
  height:30px;
  text-align:center;
  font-size:20px;
  font-family:fantasy;
  color:white;
}

.addButton{
  border:solid #f87e6e;
  border-radius:30px;
  background-color:#fda78d;
  width:30px;
  height:30px;
  text-align:center;
  font-size:20px;
  font-family:fantasy;
  color:white;
}

</style>
