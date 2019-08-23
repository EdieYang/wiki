<template>
  <d2-container>
    <template slot="header">标题：{{title}}</template>
    <div class="edit_container">
      <!-- 图片上传组件辅助-->
      <el-upload class="avatar-uploader" :action="serverUrl" name="img" :limit="1" :show-file-list="false" accept="image/jpeg,image/gif,image/png" :on-success="uploadSuccess" :on-error="uploadError" :before-upload="beforeUpload">
      </el-upload>
      <el-row v-loading="quillUpdateImg">
        <quill-editor class="editor" v-model="content" ref="myQuillEditor" :options="editorOption" @blur="onEditorBlur($event)" @focus="onEditorFocus($event)" @change="onEditorChange($event)">
        </quill-editor>
      </el-row>
      <el-button type="primary" round v-on:click="saveHtml" class="save_btn">保存</el-button>
    </div>
  </d2-container>
</template>

<script>
import axios from 'axios'
const baseUrl = 'https://www.linchongpets.com/lpCmsTest/wiki'
export default {
  name: 'page-demo',
  data () {
    return {
      content: '',
      title: '',
      quillUpdateImg: false,
      serverUrl: baseUrl + '/uploadImg',
      editorOption: {
        scrollingContainer: 'edit',
        bounds: '.edit_container',
        theme: 'snow',
        placeholder: '请输入百科信息',
        modules: {
          toolbar: {
            container: [
              ['bold', 'underline'],
              ['blockquote'],
              [{ 'list': 'ordered' }, { 'list': 'bullet' }],
              [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
              [{ 'color': [] }, { 'background': [] }],
              [{ 'align': [] }],
              ['image']
            ],
            handlers: {
              'image': function (value) {
                if (value) {
                  document.querySelector('.avatar-uploader input').click()
                } else {
                  this.quill.format('image', false)
                }
              }
            }
          }
        }
      }
    }
  },
  mounted () {
    console.log(this.$route.params.articleId)
    console.log(this.$route.params.type)
    axios.get(baseUrl + '/article?catalogListId=' + this.$route.params.articleId + '&type=' + this.$route.params.type + '&catalogId=' + this.$route.params.catalogId)
      .then(response => {
        this.content = response.data.data.content
        this.title = response.data.data.title
        this.articleId = response.data.data.id
      }, err => {
        console.log(err)
      }).catch((error) => {
        console.log(error)
      })
  },
  computed: {
    editor () {
      return this.$refs.myQuillEditor.quill
    }
  },
  methods: {
    onEditorReady (editor) { // 准备编辑器
    },
    onEditorBlur () { }, // 失去焦点事件
    onEditorFocus () { }, // 获得焦点事件
    onEditorChange ({ editor, html, text }) {
      this.content = html
    }, // 内容改变事件
    saveHtml: function () {
      console.log(this.content)
      let params = { 'id': this.articleId, 'content': this.content }
      axios({
        url: baseUrl + '/article',
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        data: JSON.stringify(params)
      }).then(response => {
        console.log(response)
        this.$message({
          message: '保存成功！',
          type: 'success'
        })
      }, err => {
        console.log(err)
      }).catch((error) => {
        console.log(error)
      })
    },
    // 富文本图片上传前
    beforeUpload (file) {
      // 显示loading动画
      this.quillUpdateImg = true
      const isIMAGE = file.type === 'image/jpeg' || 'image/gif' || 'image/png'
      const isLt1M = file.size / 1024 / 1024 < 1
      if (!isIMAGE) {
        this.$message.error('上传文件只能是图片格式!')
      }
      if (!isLt1M) {
        this.$message.error('上传文件大小不能超过 1MB!')
      }
      // loading动画消失
      this.quillUpdateImg = false
      return isIMAGE && isLt1M
    },

    uploadSuccess (res, file) {
      // res为图片服务器返回的数据
      // 获取富文本组件实例
      let quill = this.$refs.myQuillEditor.quill
      // 如果上传成功
      if (res.data !== null) {
        // 获取光标所在位置
        let length = quill.getSelection().index
        // 插入图片  res.info为服务器返回的图片地址
        quill.insertEmbed(length, 'image', 'https://pic.linchongpets.com/' + res.data)
        // 调整光标到最后
        quill.setSelection(length + 1)
      } else {
        this.$message.error('图片插入失败')
      }
      // loading动画消失
      this.quillUpdateImg = false
    },

    // 富文本图片上传失败
    uploadError () {
      // loading动画消失
      alert(111)
      this.quillUpdateImg = false
      this.$message.error('图片插入失败')
    }
  }
}
</script>
<style>
.edit_container {
  width: 396px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.editor{
  background-color: #fff;
}
.save_btn{
  margin:30px auto!important;
  width:200px;
}
</style>
