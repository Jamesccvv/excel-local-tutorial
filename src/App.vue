<template>
  <div id="app">
    <!--<img alt="Vue logo" src="./assets/logo.png">-->
    <!--<HelloWorld msg="Welcome to Your Vue.js App"/>-->
    <div>hello world</div>
    <!--上传excel-->
    <input type="file" accept=" application/vnd.openxmlformats-officedocument.spreadsheetml.sheet" id="upload_excel" @change="handleFile($event)"/>
    <!--展示excel-->
    <div id="content"></div>
  </div>
</template>

<script>
 import XLSX from 'xlsx'
// import HelloWorld from './components/HelloWorld.vue'
/* eslint-disable */
export default {
    data () {
        return {
          fileList: [],  // 获取存本地的数据
          workBook: ''
        }
    },
    methods: {
      handleFile (event) {
          console.log('查看', event.target.files)
          var fileReader = new FileReader()
          // 将excel数据转换成二进制再利用xslx插件来转换成js对象
          fileReader.readAsBinaryString(event.target.files[0])
          fileReader.onload = (e) => {
              var data = e.target.result
              this.workBook = XLSX.read(data, {
                  type: 'binary'
              })
              this.fileList = XLSX.utils.sheet_to_json(this.workBook.Sheets[this.workBook.SheetNames[0]])
              console.log(this.fileList)
          }
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
  margin-top: 60px;
}
</style>
