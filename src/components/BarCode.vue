<template>
  <div class="barcode">
    <svg :id="imgId" >
    </svg>
  </div>
</template>

<script>
//import { BrowserQRCodeSvgWriter } from '@zxing/library';
import JsBarcode from 'jsbarcode';

export default {
  name: 'BarCode',
  data: function(){
    return {
      imgId: this.generateId(),
    };
  },
  props: {
    code: String,
    width: Number,
    height: Number,
    format: String,
  },
  mounted: function(){
    this.renderBarCode();
  },
  methods: {
    renderBarCode: function(){
      JsBarcode('#' + this.imgId, this.code, {
        width: this.width,
        height: this.height,
        format: this.format,
      });
      return true;
    },
    generateId: function(){
      return 'barcode_' + parseInt(Math.random() * 100000000);
    }
  },
  watch: {
    update: function(){
      this.renderBarCode();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
