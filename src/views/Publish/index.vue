<script setup>
import { ref } from 'vue'
import {useRouter} from "vue-router";
import {ElMessage} from "element-plus";
// import Editor from "@tinymce/tinymce-vue";
import {publishArticleAPI} from "@/apis/article.js";
import { useUserStore } from "@/stores/user.js";
import UploadImg from '@/components/UploadImg/index.vue'
import TEditor from "@/components/Editor/Editor.vue";

const router = useRouter()
const userStore = useUserStore()
// 1、表单对象
const form = ref({
  title: '',
  content: '',
  cover_img: '',
  summary: ''
})

const submit = async () => {
  await publishArticleAPI(form.value.title,form.value.content, userStore.userInfo.user.uid, form.value.cover_img,form.value.summary)
  ElMessage({type:'success', message: '文章添加成功'})
  await router.push('/')
}

// const content = ref("");
// const tiny = reactive({
// init: {
//   setup: (editor) => {
//     editor.on('input change',function (){
//       form.value.content = tinymce.activeEditor.getContent({ format: "html" })
//     })
//   },
//   language: "zh_CN", //语言类型
//   placeholder: "尽情创作吧~", //textarea中的提示信息
//   min_width: 320,
//   min_height: 220,
//   content_css: "src/styles/tinymce-custom-editor.css",
//   height: 500, //注：引入autoresize插件时，此属性失效
//   resize: "both", //编辑器宽高是否可变，false-否,true-高可变，'both'-宽高均可，注意引号
//   branding: false, //tiny技术支持信息是否显示
//   statusbar: true,  //最下方的元素路径和字数统计那一栏是否显示
//   elementpath: true, //元素路径是否显示
//   draggable_modal: true,
//   menubar: false,//菜单栏配置
//   //     anchor  table image alignleft aligncenter alignright alignjustify outdent indent styleselect formatselect fontselect fontsizeselect bullist numlist indent2em lineheight formatpainter axupimgs h1 h2 h3 h4 h5 h6
//   // menubar: "file edit my1", //菜单栏配置，设为false则隐藏，不配置则默认显示全部菜单，也可自定义配置--查看 http://tinymce.ax-z.cn/configure/editor-appearance.php --搜索“自定义菜单”
//   plugins: 'fullpage code preview searchreplace autolink directionality visualblocks visualchars fullscreen image link media' +
//       ' code codesample table charmap pagebreak nonbreaking anchor insertdatetime advlist lists wordcount' +
//       ' autosave autoresize accordion',//插件配置，因为一些功能需要插件
//   toolbar: ['fullscreen undo redo restoredraft insertdatetime cut copy paste pastetext fontfamily fontsize forecolor backcolor bold italic underline strikethrough link anchor  table image alignleft aligncenter alignright alignjustify outdent indent styleselect formatselect fontselect fontsizeselect bullist numlist indent2em lineheight formatpainter axupimgs h1 h2 h3 h4 h5 h6',
//     'blockquote subscript superscript removeformat media charmap emoticons hr pagebreak insertdatetime print preview fullpage code codesample searchreplace',
//   ],
//   // images_upload_url: '/apib/api-upload/uploadimg',  //后端处理程序的url，建议直接自定义上传函数image_upload_handler，这个就可以不用了
//   // images_upload_base_path: '/demo',  //相对基本路径--关于图片上传建议查看--http://tinymce.ax-z.cn/general/upload-images.php
//   fontsize_formats: '12px 14px 16px 18px 24px 36px 48px 56px 72px',
//   font_formats: '微软雅黑=Microsoft YaHei,Helvetica Neue,PingFang SC,sans-serif;苹果苹方=PingFang SC,Microsoft YaHei,sans-serif;宋体=simsun,serif;仿宋体=FangSong,serif;黑体=SimHei,sans-serif;Arial=arial,helvetica,sans-serif;Arial Black=arial black,avant garde;Book Antiqua=book antiqua,palatino;',
//   paste_data_images: true, //图片是否可粘贴
//   file_picker_types: "image", //file image media分别对应三个类型文件的上传：link插件，image和axupimgs插件，media插件。想屏蔽某个插件的上传就去掉对应的参数
//   // 文件上传处理函数
//   automatic_uploads: false,
//   // images_upload_handler(blobInfo: any, success: any, failure: any) {
//   //   return new Promise((resolve, reject) => {
//   //     console.log(blobInfo.base64())
//   //     resolve('data:' + blobInfo.blob().type + ';base64,' + blobInfo.base64());
//   //   })
//   // }
// }
// })

const files = ref([])

</script>

<template>
  <div class="publish-article-title"><a href="/">&lt; 发布文章</a></div>
  <div class="publish-article-container container">
    <div class="left">
      目录
      <br>
      <br>
      <br>
      为文内增加标题，这里将生成目录
      <br>
      <br>
      <br>
      程序员小哥正在加班开发中
    </div>
    <div class="right">
      <el-form :model="form">
        <el-form-item>
          <input class="article-title" v-model="form.title" placeholder="请输入文章标题（5～100个字）" />
        </el-form-item>
        <el-form-item>
<!--          <Editor v-model="content" api-key="znm35mtysrhghs059pa4iacbmxhyrsjoxhybvknrl9offdxp" :init="tiny.init"/>-->
          <TEditor v-model="form.content" />
        </el-form-item>
        <el-form-item class="shangchaun" label="上传封面">
          <UploadImg :showDelete="true" :files="files">
            <template v-slot:trigger="slotProps">
              <div class="preview-container" v-if="!slotProps.myCoverImg">
                <i class="iconfont icon-tianjia" style="cursor: pointer"></i>
                <div>添加文章封面</div>
              </div>
              <div class="preview-container" v-else>
                <el-image :src="form.cover_img=slotProps?.myCoverImg?slotProps.myCoverImg:''" fit="cover"></el-image>
              </div>
            </template>
          </UploadImg>
        </el-form-item>
        <el-form-item label="文章摘要">
          <textarea v-model="form.summary"></textarea>
        </el-form-item>
        <el-form-item>
          <el-button @click="submit" class="publish-btn">立即发布</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>

</template>

<style lang="scss" scoped>
.publish-article-title {
  width: 100%;
  height: 50px;
  background-color: #ffffff;
  line-height: 50px;
  margin-left: 90px;
  margin-top: 15px;
  font-size: 17px;
}
.publish-article-container {
  display: flex;
  margin-top: 50px;
}
.publish-article-container .left {
  flex: 1;
  margin-left: 50px;
  margin-right: 20px;
  background-color: #f8f8f8;
}
.publish-article-container .right {
  flex: 5;
  margin-left: 50px;
  margin-right: 350px;
  background-color: #ffffff;
  border: 1px solid #cccccc;

}

.article-title {
  border: none; /* 移除边框 */
  background: transparent; /* 透明背景 */
  padding: 15px;
  outline: none; /* 移除聚焦时的轮廓 */
  box-shadow: none; /* 无阴影 */
  font-family: Arial, sans-serif; /* 设置字体 */
  font-size: 22px; /* 设置字号 */
  line-height: 1.5; /* 设置行高 */
  white-space: nowrap; /* 文本不换行 */
  border-bottom: 1px solid #43341b;
  cursor: text; /* 显示文本光标 */
  min-width: 100px; /* 设置最小宽度，防止输入框过窄 */
  width: 100%;
}


.preview-container {
  height: 120px;
  width: 200px;
  background-color: #eaeaea;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.cover-img {
  height: 120px;
  width: 200px;
  position: absolute;
}
</style>
