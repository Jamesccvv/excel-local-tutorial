<template>
  <div id="app">
    <!--<img alt="Vue logo" src="./assets/logo.png">-->
    <!--<HelloWorld msg="Welcome to Your Vue.js App"/>-->
    <div>hello world</div>
    <!--上传excel-->
    <input type="file" accept=" application/vnd.openxmlformats-officedocument.spreadsheetml.sheet" id="upload_excel" @change="handleFile($event)"/>
    <!--展示excel-->
    <div id="content"></div>
    <div>阿拉伯数字转汉字</div>
    <!--<div>{{numberToChinese(12)}}</div>-->
  </div>
</template>

<script>
 import XLSX from 'xlsx'

 // 阿拉伯数字转中文
 var chnNumChar = ['零', '一', '二', '三', '四', '五', '六', '七', '八', '九']
 var chnUnitSection = ['', '万', '亿', '万亿', '亿亿']
 var chnUnitChar = ['', '十', '百', '千']

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
          /**
           * 利用了h5 的file相关api
           * 和xslx插件来实现此功能
           */
          fileReader.readAsBinaryString(event.target.files[0])
          fileReader.onload = (e) => {
              var data = e.target.result
              this.workBook = XLSX.read(data, {
                  type: 'binary'
              })
              this.fileList = XLSX.utils.sheet_to_json(this.workBook.Sheets[this.workBook.SheetNames[0]])
              console.log(this.fileList)
          }
    },


    // 阿拉伯数字转汉字算法
        /**
         * 每个计数数字都跟着一个权位，权位有：十，百，千，万，亿。
         * 以万为小节，对应一个节权位，万以下没有节权位。
         * 每个小节内部以“十百千”为权位独立计数
         * “十百千”不能连续出现，而“万”和“亿”作为节权位时可以和其他权位连用
         */
        // 中文数字“零”的规则
        /**
         * 以10000为小节，小节的结尾即使是0，也不使用零
         * 小节内两个非0数字之间要使用“零”
         * 当小节的“千”位是0时（即：1-999），只要不是首小节，都要补“零”
         */
        // 节内转换
        sectionToChinese (section) {
            let strIns = ''
            let chnStr = ''
            let unitPos = 0
            let zero = true
            while (section > 0) {
                let v = section % 10
                if (v === 0) {
                    if (!zero) {
                        zero = true
                        chnStr = chnNumChar[v] + chnNumChar
                    } else {
                        zero = false
                        strIns = chnNumChar[v]
                        strIns += chnUnitChar[unitPos]
                        chnStr = strIns + chnStr
                    }
                    unitPos ++
                    section = Math.floor(section / 10)
                }
            }
            return chnStr
        },
        // 阿拉伯数字转汉字算法
        numberToChinese (num) {
            let unitPos = 0
            let strIns = ''
            let chnStr = ''
            let needZero = false

            if (num === 0) {
                return chnNumChar[0]
            }

            while (num > 0) {
                let section = num % 10000
                if (needZero) {
                    chnStr = chnNumChar[0] + chnStr
                }
                strIns = this.sectionToChinese(section)
                strIns += (section !== 0) ? chnUnitSection[unitPos] : chnUnitSection[0]
                chnStr = strIns + chnStr
                needZero = (section < 1000) && (section > 0)
                num = Math.floor(num / 10000)
                unitPos++
            }
            return chnStr
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
