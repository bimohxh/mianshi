<template lang="pug">
  div.main
    div.headbar
      div.left
        h3 Vue.js 入门到精通
      div.middle
        div.view-vis
          a(href="javascript:void(0)" @click="isLeftShow = !isLeftShow" v-bind:class="'oper-btn on-' + isLeftShow" style="border-right: 0;")
            // icon(name="navicon" width="16") 目录
          // a(href="javascript:void(0)" @click="isRealTimePreview = !isRealTimePreview" :class="'oper-btn on-' + isRealTimePreview") 代码
          a(href="javascript:void(0)" @click="isRightShow = !isRightShow" v-bind:class="'oper-btn on-' + isRightShow")
            // icon(name="eye" width="15") 预览

      div.right
        a(href="javascript:void(0)" @click="isRealTimePreview = !isRealTimePreview" v-bind:class="'oper-btn on-' + isRealTimePreview") 实时预览
        a(href="javascript:void(0)" @click="run")
          icon(name="run")
        
    div.editor-box
      div.left(v-show="isLeftShow")
        div.catalog-box
          div.cate-group(v-for="cat in catalogs")
            div.cate-1 {{cat.name}}
            div.cate-2(v-for="sub in cat.subs")
              a(href="") {{sub.name}}

      div.middle
        div.code-box
          textarea(id="code" name="code")
          

        
      div.right(v-show="isRightShow")
        div.preview-box
          iframe#preview

</template>

<script>
import axios from '~/plugins/axios'

const initHtml = `<!doctype html>
<html>
  <head>
      <!-- CSS 和 JS 外链 -->

      <!--内联 CSS  -->
      <style type="text/css">

        /* CSS 样式 */

      </style>
  </head>

  <body>

    <!-- JS 调用 -->

  </body>
</html>`

var editor
export default {
  data () {
    return {
      isRealTimePreview: false,
      isLeftShow: true,
      isRightShow: false,
      catalogs: [
        {
          name: '基础用法',
          subs: [
            {
              name: '基本调用'
            }
          ]
        },
        {
          name: '方法',
          subs: [
            {
              name: '获取代码'
            },
            {
              name: '设置值'
            },
            {
              name: '播放视频'
            },
            {
              name: '暂停'
            },
            {
              name: '重复播放'
            }
          ]
        },
        {
          name: '事件',
          subs: [
            {
              name: '播放结束时'
            },
            {
              name: '播放开始时'
            }
          ]
        },
        {
          name: '进阶',
          subs: [
            {
              name: '自定义插件'
            }
          ]
        }
      ],
      comcon: initHtml
    }
  },
  components: {
  },
  watch: {
    comcon: function () {
      if (this.isRealTimePreview) {
        this.run()
      }
    }
  },
  methods: {
    fetchCatalogs: async function () {
      let res = await axios().get('feex/catalog')
      this.catalogs = res.data
    },
    run: function () {
      let _html = this.comcon
      let previewFrame = document.getElementById('preview')
      var preview = previewFrame.contentDocument || previewFrame.contentWindow.document
      preview.open()
      preview.write(_html)
      preview.close()
      this.isRightShow = true
    }
  },
  mounted () {
    var CodeMirror = require('codemirror')
    require('codemirror/lib/codemirror.css')
    require('codemirror/addon/edit/closetag.js')
    require('codemirror/addon/fold/xml-fold.js')
    require('codemirror/mode/xml/xml.js')
    require('codemirror/mode/javascript/javascript.js')
    require('codemirror/mode/css/css.js')
    require('codemirror/mode/htmlmixed/htmlmixed.js')
    require('codemirror/addon/display/fullscreen.css')
    require('codemirror/addon/display/fullscreen.js')

    editor = CodeMirror.fromTextArea(document.getElementById('code'), {
      mode: 'text/html',
      autoCloseTags: true,
      inputStyle: 'contenteditable',
      lineWrapping: true,
      viewportMargin: Infinity,
      extraKeys: {
        'F11': function (cm) {
          cm.setOption('fullScreen', !cm.getOption('fullScreen'))
        },
        'Esc': function (cm) {
          if (cm.getOption('fullScreen')) cm.setOption('fullScreen', false)
        }
      }
    })
    let _self = this
    editor.on('change', editor => {
      _self.comcon = editor.getValue()
    })

    editor.setValue(initHtml)
  }
}
</script>

<style lang="scss">
  .main {
    padding-top: 60px;
    .headbar {
      height: 50px;
      position: fixed;
      width: 100%;
      top: 0;
      background-color: #03A9F4;
      z-index: 10;
      display: flex;

      * {
        color: #FFF;
        fill: #FFF;
      }

      .right, .left, .middle {
        display: flex;
        align-items: center;
      }

      .left {
        flex-grow: 1;
        padding: 0 20px;
      }

      .right {
        padding: 0 20px;
        & > * {
          margin-left: 20px;
        }
      }

      .middle {
        flex-grow: 1
      }

      .oper-btn {
        padding: 5px 5px;
        text-decoration: none;
        border: #FFF 1px solid;
        font-size: 12px;

        &.on-true {
          background-color: #e79215
        }
      }
    }
    .editor-box {
      display: flex;
      .left {
        width: 300px;
        flex-shrink: 0;

        .catalog-box {
          position: fixed;
          background-color: #FFF;
          top: 60px;
          bottom: 10px;
          width: 300px;
          left: 0;
          box-shadow: 0 1px 2px 0 rgba(0,0,0,.05);
          overflow-y: auto;
          .cate-group {
            margin-bottom: 20px;
          }
          .cate-1 {
            color: #DDD;
          }
          
          .cate-2 {
            border-bottom: #EEE 1px solid;
            border-bottom: 1px solid #eeeeee52;
            padding: 8px 0;
            a {
              color: #333;
              text-decoration: none;
            }
          }
        }
      }

      .middle {
        flex-grow: 1;
        word-wrap: break-word;
        width: 0;
        padding: 10px;
        padding-top: 0;

        .code-box {
          width: 100%;
          max-width: 800px;
          margin: 0 auto;
          background-color: #fff;
          border-radius: 2px;
          padding: 10px;
          box-shadow: 0 1px 2px 0 rgba(0,0,0,.05);
          flex-grow: 1;
        }
      }

      .right {
        position: relative;
        word-wrap: break-word;
        width: 500px;
        flex-shrink: 0;

        .preview-box {
          position: fixed;
          background-color: #FFF;
          top: 60px;
          bottom: 10px;
          width: 500px;
          right: 0;
          box-shadow: 0 1px 2px 0 rgba(0,0,0,.05);

          iframe {
            width: 100%;
            height: 100%;
            border: none;
          }
        }
      }
    }

    .catalog-box {
      position: relative;
      width: 300px;
      padding: 20px;
      bottom: 50px;
      background-color: #FFF;
      flex-shrink: 0;
      margin-right: 10px;
    }
  }
</style>
