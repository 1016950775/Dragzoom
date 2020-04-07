<template>
  <!-- https://github.com/gorkys/vue-draggable-resizable-gorkys/blob/master/README_ZH.md#%E5%9C%A8%E7%BA%BF%E6%BC%94%E7%A4%BA  文档地址 -->
  <!-- npm install --save vue-draggable-resizable  npm安装 -->
  <!-- import VueDraggableResizable from 'vue-draggable-resizable'
    import 'vue-draggable-resizable/dist/VueDraggableResizable.css'
  Vue.component('vue-draggable-resizable', VueDraggableResizable)     main.js引入-->

  <div class="home">
    <!-- 标题时间 -->
    <el-form ref="Newsform" :model="Newsform" label-width="100px">
      <el-form-item label="活动名称：">
        <el-input size="mini" v-model="Newsform.title"></el-input>
      </el-form-item>
      <el-form-item label="活动时间：">
        <el-date-picker
          type="date"
          size="mini"
          placeholder="选择日期"
          v-model="Newsform.time"
          style="width: 100%;"
        ></el-date-picker>
      </el-form-item>
    </el-form>
    <span style="color:red">上传图片尺寸为421px(宽)×622px(高)</span>
    <!--  -->
    <div>
      <el-button size="mini" @click="addban">添加版面</el-button>
      <el-button size="mini" @click="Preservation">保存</el-button>
      <el-button size="mini" @click="Reset">重置</el-button>
      <el-button size="mini" @click="cancel">取消</el-button>
    </div>
    <!-- 版面 -->
    <div v-for="(item,index) in Page" :key="index" style="border:1px solid red;margin: 20px 0;">

      <div style="text-align: left;text-indent: 20px;margin: 10px 0;">
        <span>版面标题：</span>
        <el-input style="width: 200px;margin-right:30px" size="mini" v-model="item.title" placeholder="请输入版面标题"></el-input>
        <el-button size="mini" @click="delnew(index)">删除版面</el-button>
        <el-button size="mini" @click="addnew(index)">导入信息</el-button>
      </div>

      <div style="text-align: left;text-indent: 20px;display: flex;line-height: 35px;">
        <div>版面图片：</div>
        <el-upload
        style="display:flex"
        ref="upload"
        :limit="1"
        action="https://jsonplaceholder.typicode.com/posts/"
        :on-remove="handleRemove"
        :on-change="handleChange"
        :file-list="fileList"
        :auto-upload="false"
      >
        <el-button slot="trigger" size="mini" type="primary" @click="xuanze(index)">选取文件</el-button>
      </el-upload>
      </div>

      <!-- 报纸区域 -->
      <div style="height: 622px; width: 421px; position: relative; margin:10px auto;border: 1px solid #ccc;">
        <img style="height: 622px; width: 421px;" :src="item.image" alt />
        <vue-draggable-resizable
          v-for="(item1,index1) in item.form"
          :key="index1"
          :w="100"
          :h="100"
          :min-width="50"
          :min-height="50"
          @dragging="onDrag"
          @resizing="onResize"
          @activated="onActivated(index,index1)"
          :parent="true"
          style="cursor: move;"
        >
          <div :style="{ width: item1.width+'px' , height: item1.height+'px' , overflow: 'hidden'}">
            <div style="text-align: right;cursor: pointer;" @click="del(index,index1)">❌</div>
            <div class="text" :style="{width: (item1.width-2)+'px'}">
              {{item1.name}}
              <br />
              X: {{ item1.x }} / Y: {{ item1.y }} - Width: {{ item1.width }} / Height: {{ item1.height }}
            </div>
          </div>
        </vue-draggable-resizable>
      </div>
      <!-- 报纸结束 -->

    </div>
    <!-- 版面结束 -->
  </div>
</template>

<script>
import VueDraggableResizable from "vue-draggable-resizable";
import "vue-draggable-resizable/dist/VueDraggableResizable.css";
export default {
  name: "home",
  components: {},
  data: function() {
    return {
      fileList: [],//储存上传的图片

      uploadid: 0,//储存page的index

      // 版面数据
      Page: [
        {
          title: "",
          image: "",
          form: [
            { name: "第一个", width: 100, height: 100, x: 0, y: 0 },
            {
              name: "第二个",
              width: 100,
              height: 100,
              x: 0,
              y: 0
            }
          ]
        }
      ],

      // 标题时间
      Newsform: {
        title: "",
        time: ""
      },

      num: 2,//模拟计数

      index: 0, //版面index
      index1: 0, //信息index

      
    };
  },
  methods: {
    // 改变大小
    onResize(x, y, width, height) {
      console.log(x, "x轴");
      console.log(y, "y轴");
      console.log(width, "改变宽");
      console.log(height, "改变高");

      this.Page[this.index].form[this.index1].x = x;
      this.Page[this.index].form[this.index1].y = y;
      this.Page[this.index].form[this.index1].width = width;
      this.Page[this.index].form[this.index1].height = height;
    },

    // 拖动时
    onDrag(x, y) {
      console.log(x, "改变x轴");
      console.log(y, "改变y轴");

      this.Page[this.index].form[this.index1].x = x;
      this.Page[this.index].form[this.index1].y = y;
    },

    // 点击时
    onActivated(index, index1) {
      console.log(index, "拿到的index值");
      console.log(index1, "拿到的index1值");
      this.index = index;
      this.index1 = index1;
    },

    // 添加拖拽缩放框
    addnew(index) {
      console.log(index, "给那个添加一个");
      console.log(this.Page, "要添加的一大块");
      this.num++;
      let obj = {
        name: "第" + this.num + "个",
        width: 100,
        height: 100,
        x: 0,
        y: 0
      };
      this.Page[index].form.push(obj);
    },

    // 删除一块
    del(index, index1) {
      console.log(index, index1, "删除单个");
      this.Page[index].form.splice(index1, 1);
    },

    // 删除一大块
    delnew(index) {
      this.Page.splice(index, 1);
    },

    // 添加版面
    addban() {
      let frame = {
        title: "",
        image: "",
        form: []
      };
      this.Page.push(frame);
    },

    // 删除上传文件
    handleRemove(file, fileList) {
      console.log(file, fileList);
      this.fileList.slice(file, 1);
    },
    // 选择文件确定时触发
    handleChange(file, fileList) {
      console.log(file, "选择的文件的数据");
      this.Page[this.uploadid].image = window.URL.createObjectURL(file.raw);
      console.log(this.Page[this.uploadid].image, "这是上传的图片");
      console.log(this.fileList, "储存在列表的数据");
    },

    // 选择文件按钮
    xuanze(index) {
      console.log(index, "第几个版面的index");
      this.uploadid = index;
      console.log(this.uploadid, "版面的index值");
    },

    // 保存
    Preservation(){
      alert("保存了")
    },

    // 重置
    Reset(){
      alert("重置了")
    },

    // 取消
    cancel(){
      alert("取消了")
    },
  }
};
</script>
<style scoped>
.home {
  width: 650px;
}
/* 组件的框 */
.vdr {
  border: 1px solid #000;
  background: rgba(138, 255, 100, .5);
}

/* 框里字的样式 */
.text {
  background-color: rgba(255,255,255,.7);
  text-align: left;
  color: #2b2b2b;
  font-weight: bold;
}
</style>