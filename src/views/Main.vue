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
              <th>内訳</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="history in total_history" :key="history.id">
              <td>{{history.date}}</td>
              <td>{{history.price}}円</td>
              <td>{{history.detail}}</td>
            </tr>
          </tbody>
        </table>
        <button class="history_download_button" id="download" type="button" @click="downloadCSV">履歴のダウンロード</button>
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
        <input class="import_csv_button" @change="fileChange" type="file" id="file_input_expense" name="file_input_expense">
      </div>
      <div class="previewScreen">
        <div class="previewContents">
          <h2>合計金額：{{totalPrice}}円</h2>
          <h3>お預かり：<input class="outOfForm" v-model="outOf">円</h3>
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

    let item_list=ref([
      // {name:"やきそば(塩)",price:600,remarks:"",amount:0},
      // {name:"やきそば(ソース)",price:650,remarks:"！オイスターソースには大豆が含まれています！",amount:0}
    ])

    // let cartItemList=ref([
    // ])

    let displayStyle = ref([
      {D_style:"none"},
      {D_style:"block"}
    ])

    let total_history = ref([])

    let subTabCtrl = ref(document.getElementById("subTab"))

    const addItem=(i)=>{
      totalPrice.value = totalPrice.value + Number(i.price);
      i.amount=Number(i.amount)+1;
      // cartItemList.value.push(i.name)
    }

    const reduceItem=(i)=>{
      totalPrice.value = totalPrice.value - Number(i.price);
      i.amount=Number(i.amount)-1
      // cartItemList.value.push(i.price)
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

// CSV出力
    const json2csv=(json)=> {
        var header = Object.keys(json[0]).join(',') + "\n";

        var body = json.map(function(d){
            return Object.keys(d).map(function(key) {
                return d[key];
            }).join(',');
        }).join("\n");

        return header + body;
    }

    const downloadCSV=()=> {
        //ダウンロードするCSVファイル名を指定する
        let dateObj = new Date()
        let now_date = getDate(dateObj);
        const filename = now_date+".csv";
        //CSVデータ
        const data = json2csv(total_history.value);
        //BOMを付与する（Excelでの文字化け対策）
        const bom = new Uint8Array([0xef, 0xbb, 0xbf]);
        //Blobでデータを作成する
        const blob = new Blob([bom, data], { type: "text/csv" });

        //IE10/11用(download属性が機能しないためmsSaveBlobを使用）
        if (window.navigator.msSaveBlob) {
            window.navigator.msSaveBlob(blob, filename);
        //その他ブラウザ
        } else {
            //BlobからオブジェクトURLを作成する
            const url = (window.URL || window.webkitURL).createObjectURL(blob);
            //ダウンロード用にリンクを作成する
            const download = document.createElement("a");
            //リンク先に上記で生成したURLを指定する
            download.href = url;
            //download属性にファイル名を指定する
            download.download = filename;
            //作成したリンクをクリックしてダウンロードを実行する
            download.click();
            //createObjectURLで作成したオブジェクトURLを開放する
            (window.URL || window.webkitURL).revokeObjectURL(url);
        }
    }


// CSVファイルの読み込み処理
    const fileChange=(e)=> {
      const file = e.target.files[0];
      const reader = new FileReader();
      reader.readAsText(file, 'Shift_JIS');
      // const CSVs = [];

      const loadFunc = () => {
        const lines = reader.result.split("\n");
        lines.forEach((element, index) => {
          if( index === 0 ){ return; }
          const CSV_data = element.split(",");
          // if (CSV_data.length != 3) return;
          const CSV = {
            name: CSV_data[0],
            price: CSV_data[1],
            remarks: CSV_data[2],
            amount: CSV_data[3]
          };

          item_list.value.push(CSV);
          // item_list.value = item_list.value.shift;
          console.log(item_list.value[0])
          // item_list.value.delete(1,3)
          console.log("CSV",CSV)
        });
        // this.CSVs = CSVs;
      };
      reader.onload = loadFunc;
      reader.readAsBinaryString(file);
      console.log("item_list:",item_list.value)
    }


// 会計処理
    const getDate=(dateObj)=>{
      let now_date = dateObj.getFullYear() + '年' + 
      ('00' + (dateObj.getMonth() + 1)).slice(-2) + '月' + 
      ('00' + dateObj.getDate()).slice(-2) + '日' + 
      ('00' + dateObj.getHours()).slice(-2) + '時' + 
      ('00' + dateObj.getMinutes()).slice(-2) + '分';
      return now_date;
    }

    const checkButton=()=>{
      let dateObj = new Date()
      let now_date = getDate(dateObj);
      total_history.value.push({date: now_date, price: totalPrice.value, detail:getDetail()})
      totalPrice.value = 0
      outOf.value = 0
      change.value = 0
      cashierCount.value++
      resetAmount();

      nowCheckTabStyle.value = "none"
    }

    const getDetail=()=>{
      let bought_items = ""
      for(let i=0; i<item_list.value.length; i++){
        if(item_list.value[i].amount == 0){
          break;
        }else{
          bought_items = bought_items + item_list.value[i].name + "×" + item_list.value[i].amount + "/";
        }
      }
      return bought_items;
    }

    const resetAmount=()=>{
      for(let i=0; i<item_list.value.length; i++){
        item_list.value[i].amount = 0;
      }
    }

    const returnButton=()=>{
      nowCheckTabStyle.value = "none"
    }

    return{
      totalPrice,
      outOf,
      change,
      item_list,
      total_history,
      amount,
      subTabCtrl,
      displayStyle,
      nowSubTabStyle,
      nowCheckTabStyle,
      cashierCount,
      // codeArray,
      addItem,
      reduceItem,
      cashier,
      openHistory,
      getDate,
      resetAmount,
      checkButton,
      returnButton,
      getDetail,
      downloadCSV,
      json2csv,
      fileChange,
      // onloadHandler,
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

.history_download_button{
  border:solid white 1px;
  border-radius:10px;
  margin-bottom:5px;
}

.import_csv_button{
  margin-left:5px;
}

</style>
