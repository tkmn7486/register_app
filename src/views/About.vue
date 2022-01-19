<template>
  <div class="about">
    <div class="mainTab" id="mainTab">
      <div class="titleBar">
        <div class="title">
          <h1>ヘルプ(開発中)</h1>
        </div>
      </div>
    </div>

    <div class="help" v-for="help_content in help_contents" :key="help_content.id">
      <div class="help_contents">
        <h2>{{help_content.title}}</h2>
        <div class="detail" id="help_content.id">
          <p>{{help_content.details}}</p>
        </div>
      </div>
    </div>

    <div class="contact_contents">
      <div class="contact">
        <h2>バグ報告・ご要望</h2>
        <h4>以下メールアドレスにご連絡ください。</h4>
        <h5>Mail: tkmn7486.pro@gmail.com</h5>
      </div>
    </div>
  </div>
</template>

<script>
import {ref} from 'vue'

export default {
  name:"about",
  setup(){
    let help_contents = ref([
      {id:"0", title:'説明書', details:'ここに説明書を置いておきます。'},
      {id:"1", title:'メニューの作成', details:'メニューはオンライン上で作成することができます。'}
    ])

    return {
      help_contents,
    }
  }
}
</script>

<style>
.title{
  color:white;
  padding:5px;
}

.help_contents{
  border:solid 2px;
  border-radius:10px;
  margin:10px;
  background-color:white;
}

.contact_contents{
  border:solid 2px;
  border-radius:10px;
  margin:10px;
  background-color:white;
}

.contact{
  padding:5px;
}
  
</style>