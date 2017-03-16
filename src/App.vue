<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor ref="resumeEditor" :markdown="currentMarkdown" :enableHtml="enableHtml"></ResumeEditor>
  </div>
</template>

<script>
import StyleEditor from "./components/StyleEditor"
import ResumeEditor from "./components/ResumeEditor"
import './assets/reset.css'

export default {
  name: 'app',
  components: {
    StyleEditor,
    ResumeEditor
  },
  data() {
    return {
      interval: 20,
      currentStyle: '',
      enableHtml: false,
      finalStyle: [
      `
      /*
      *大家好，我是Leenam
      *马上就毕业了，为了应聘工作我要制作一份简历
      *现在开始吧
      */

      /* 首先给所有元素加上过渡效果 */
      *{
        transition:all 0.2s;
        -webkit-transition:all 0.2s;
      }

      /* 白色背景太刺眼，咱换一个 */
      html {
        color: rgb(222,222,222);
        background: #1F1F1F;
      }

      /*我们来个边框*/
      .styleEditor {
        border: 1px solid;
        overflow: auto;
        width: 45vw;
        height: 90vh;
        margin: 5px;
        border-radius: 5px;
      }
      
      html {
        perspective: 1000px;
        -webkit-perspective: 1000px;
      }
      
      /*嗯，加点3D效果*/
      .styleEditor {
        position: fixed;
        left: 0;
        top: 0;
        -webkit-transition: none; 
        transition: none;
        -webkit-transform: rotateY(10deg) translateZ(-100px) ;
        transform: rotateY(10deg) translateZ(-100px) ;
      }

      /* 接下来我给自己准备一个编辑器 */
      .resumeEditor{
        position: fixed; right: 0; top: 0;
        padding: .5em;  margin: .5em;
        width: 48vw; height: 90vh; 
        border: 1px solid;
        background: white; color: #222;
        overflow: auto;
      }
      /* 好了，我开始写简历了 */

      `
      ,
      `
      /* 这个简历好像差点什么
      * 对了，这是 Markdown 格式的，我需要变成对 HR 更友好的格式
      * 好的，我们用开源工具翻译成 HTML 
      */
      `
      ,
      `
      /* 再对 HTML 加点样式 */
      .resumeEditor{
        padding: 2em;
      }
      .resumeEditor h2{
        display: inline-block;
        border-bottom: 1px solid;
        margin: 1em 0 .5em;
      }
      .resumeEditor ul,.resumeEditor ol{
        list-style: none;
      }
      .resumeEditor ul> li::before{
        content: '•';
        margin-right: .5em;
      }
      .resumeEditor ol {
        counter-reset: section;
      }
      .resumeEditor ol li::before {
        counter-increment: section;            
        content: counters(section, ".") " ";  
        margin-right: .5em;
      }
      .resumeEditor blockquote {
        margin: 1em;
        padding: .5em;
        background: #ddd;
      }
      `],
      currentMarkdown: '',
      fullMarkdown: `杨黎楠
----

云南农业大学 计算机科学与技术专业

----

* HTML(5)
* CSS(5)
* JavaScript(ES6)
* Vuejs
* Python

我的GitHub和博客
----
* <a href="https://lennam.github.io" target="_blank">GitHub</a>
* <a href="http://southernxtar.com" target="_blank">博客</a>

自我介绍
----
能够发现问题，并解决问题

`
    }
  },
  created() {
    this.makeResume()
  },
  methods: {
    makeResume: async function () {
      await this.writeStyle(0)
      await this.writeResume()
      await this.writeStyle(1)
      await this.showHtml()
      await this.writeStyle(2)
    },
    showHtml: function () {
      return new Promise((resolve,reject) => {
        this.enableHtml = true
        resolve()
      })
    },
    writeStyle(n) {
      return new Promise((resolve, reject) => {
        let interval = this.interval
        let showStyle = (async function () {
          let style = this.finalStyle[n]
          if (!style)  { return }
          let length = this.finalStyle.filter((_, index) => index <= n).map((item) => item.length).reduce((p, c) => p + c, 0)
          let prefixLength = length - style.length
            if (this.currentStyle.length < length) {
              let l = this.currentStyle.length - prefixLength
              let char = style.substring(l, l + 1) || ' '
              this.currentStyle += char
              if (style.substring(l - 1, l) === '\n' && this.$refs.styleEditor) {
                this.$nextTick(() => {
                  this.$refs.styleEditor.goBottom()
                })
              }
              setTimeout(showStyle, interval)
            } else {
              resolve()
            }
          }).bind(this)
          showStyle()
        })
      },
      writeResume() {
        return new Promise((resolve, reject) => {
          let length = this.fullMarkdown.length
          let interval = this.interval
          let showResume = () => {
            if (this.currentMarkdown.length < length) {
              this.currentMarkdown = this.fullMarkdown.substring(0, this.currentMarkdown.length + 1)
              let lastChar = this.currentMarkdown[this.currentMarkdown.length - 1]
              let prevChar = this.currentMarkdown[this.currentMarkdown.length - 2]
              console.log(prevChar)
              if (prevChar === '\n' && this.$refs.resumeEditor) {
                this.$nextTick(() => this.$refs.resumeEditor.goBottom())
              }
              setTimeout(showResume, interval)
            } else {
              resolve()
            }
          }
          showResume()
        })
      }
    }
  }
</script>

<style scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
html {
    min-height: 100vh;
  }
  
  * {
    -webkit-transition: all .3s;
    transition: all .3s;
  }
</style>
