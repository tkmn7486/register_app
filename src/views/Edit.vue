<template>
  <div class="Edit">
    <div class="mainTab" id="mainTab">
      <div class="titleBar">
        <h1 class="title">メニューデータ作成</h1>
      </div>
      <div class="inputForm">
        <div class="formBody">
          <div class="formContents">
            <h2>>>新規追加</h2>
            <h2>商品名:<input class="input_menu" v-model="new_menu_name"></h2>
            <h2>金額:<input class="input_menu" v-model="new_menu_price"></h2>
            <h2>詳細・備考:<input class="input_menu" v-model="new_menu_remarks"></h2>
            <button @click="addNewMenu">追加</button>
          </div>
        </div>
      </div>
      
      <div class="contents">
        <table class="new_menus">
          <tr>
            <th class="new_header">商品名</th>
            <th class="new_header">価格</th>
            <th class="new_header">詳細・備考</th>
          </tr>
          <tr v-for="new_menu in new_menus" :key="new_menu.id">
            <td>{{new_menu.name}}</td>
            <td>{{new_menu.price}}円</td>
            <td>{{new_menu.remarks}}</td>
          </tr>
        </table>
      </div>
    </div>
      <button class="menu_save_button" @click="downloadCSV">メニューを保存して出力</button>
  </div>
</template>

<script>
import {ref} from 'vue'

export default{
    name:'Edit',
    setup(){
        let new_menus = ref([
        ])
        let new_menu_name = ref()
        let new_menu_price = ref()
        let new_menu_remarks = ref()

      const addNewMenu=()=>{
        if(new_menu_remarks.value ==""){
          new_menu_remarks.value="---";
          new_menus.value.push(
              {name:new_menu_name.value, 
              price:new_menu_price.value, 
              remarks:new_menu_remarks.value, 
              amount:0})
          console.log(new_menus.value)
        }else{
          new_menus.value.push({
              name:new_menu_name.value, 
              price:new_menu_price.value, 
              remarks:new_menu_remarks.value, 
              amount:0}) 
          console.log(new_menus.value)
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
        const filename = "new_menu.csv";
        //CSVデータ
        const data = json2csv(new_menus.value);
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

        return{
            new_menus,
            new_menu_name,
            new_menu_price,
            new_menu_remarks,
            addNewMenu,
            json2csv,
            downloadCSV,
        }
    }
}

</script>

<style>
.title{
    color:white;
    padding:5px;
}

.contents{
    top: 65px;
    right:20px;
    position:absolute;
}

.new_menus{
    border:solid;
    border-radius:10px;
    background-color:white;
    padding:10px;
}

.new_header{
    padding-left:10px;
    padding-right:10px;
}

.formBody{
    display:inline-block;
    margin-top:10px;
    margin-left:10px;
    border:solid;
    border-radius:10px;
    background-color:white;
    width:50%;
}

.formContents{
    padding:10px;
}

.input_menu{
    width:300px;
    height:30px;
}

.menu_save_button{
  margin-top:10px;
  margin-left:10px;
  border:solid 3px;
  border-radius:5px;
  background-color:white;
}
</style>