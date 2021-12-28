<template>
  <div class="Edit">
    <div class="mainTab" id="mainTab">
      <div class="titleBar">
        <h1>メニューデータ作成</h1>
      </div>
    </div>
      <button @click="downloadCSV">メニューを保存して出力</button>
  </div>
</template>

<script>
import {ref} from 'vue'

export default{
    name:'Edit',
    setup(){
        let new_menu = ref([
            {name:"パン",price:500,spetial:"おいしい"}
        ])

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
        const data = json2csv(new_menu.value);
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
            new_menu,
            json2csv,
            downloadCSV,
        }
    }
}

</script>