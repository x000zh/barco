<template>
  <div id="app">
    <h1>BarCode</h1>
    <ul class="config">
      <li>Format: <select  v-model="barcodeFormat">
        <option v-for="format in barcodeFormats" :value="format">{{format}}</option>
      </select></li>
      <li>Width: <input v-model="barcodeWidth"></li>
      <li>Height:<input v-model="barcodeHeight"></li>
      <li><textarea v-model="codes" placeholder="add multiple lines" v-on:input="autoSize($event)"></textarea></li>
      <li><button v-on:click="changeCodeList">Generate</button></li>
    </ul>
    <div v-for="code in codeList" :key="code.id">
      <BarCode :code="code.code" :width="barcodeWidth" :height="barcodeHeight" :format="barcodeFormat"/>
    </div>
  </div>
</template>

<script>
import BarCode from './components/BarCode.vue'

export default {
  name: 'app',
  data: function(){
    let data = this.loadState();
    return {
      codeList: [],
      codes: null==data?'':data.codes,
      barcodeWidth: null==data?2:data.barcodeWidth,
      barcodeHeight: null==data?24:data.barcodeHeight,
      barcodeFormat: null==data?'CODE128':data.barcodeFormat,
      barcodeFormats: [
        'CODE128',
        'CODE128A',
        'CODE128B',
        'CODE128C',
        'EAN',
        'EAN-13',
        'EAN-8',
        'EAN-5',
        'EAN-2',
        'UPC(A)',
        'UPC(E)',
        'CODE39',
        'ITF',
        'ITF-14',
        'MSI',
        'MSI10',
        'MSI11',
        'MSI1010',
        'MSI1110',
        'Pharmacode',
        'Codabar',
      ],
    }
  },
  mounted: function(){
    this.changeCodeList();
  },
  components: {
    BarCode
  },
  methods: {
    changeCodeList: function(){
      let codes = this.codes;
      let codeArr = codes.split("\n");
      let ret = [];
      for(let idx in codeArr){
        let code = codeArr[idx];
        if(code.length > 0){
          let id = [code, this.barcodeWidth, this.barcodeHeight, this.barcodeFormat];
          ret.push({
            id: id.join('-'),
            code: code,
          });
        }
      }
      this.codeList = ret;
      this.changeState();
    },
    autoSize: function($event){
      let height = 20 * this.codes.split("\n").length;
      let elm = $event.srcElement;
      if(height < 40){
        height = 40;
      }
      elm.style.height = height + "px";
    },
    changeState: function(){
      let data = {
        codes: this.codes,
        barcodeWidth: this.barcodeWidth,
        barcodeHeight: this.barcodeHeight,
        barcodeFormat: this.barcodeFormat,
      };
      let query = '?data=' + encodeURIComponent(JSON.stringify(data));
      if(query != window.location.search){
        let url = window.location.href.split('?')[0] + query;
        window.history.pushState(data, null, url);
      }
    },
    loadState: function(){
      let search = window.location.search.split("?");
      let data = null;
      if(2 == search.length ){
        let vars = search[1].split('&');
        vars.forEach(function(v){
          let tmp = v.split('=');
          if('data' == tmp[0] && tmp.length > 1){
            data = JSON.parse(decodeURIComponent(tmp[1]));
          }
        })
      }
      return data;
    }
  },
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  font-size: 12px;
}
ul {
  list-style: none;
}
.config {
  width: 400px;
  text-align: left;
  margin: 0 auto;
  li {
    margin-top: 12px;
  }
  textarea {
    resize: none;
    border: 1px solid black;
    width: 100%;
    overflow-y: hidden;
    height: 40px;
    overflow-x: scroll;
    min-height: 40px;
    line-height: 20px;
  }
}
@media print {
  .config{
    display: none;
  }
}
</style>
