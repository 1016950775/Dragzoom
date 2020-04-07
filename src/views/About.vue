<template>
	<!-- https://i5ting.github.io/How-to-learn-node-correctly/   狼叔nodejs文章 -->
<!-- https://www.cnblogs.com/steamed-twisted-roll/p/9253077.html    原文地址 -->
  <div>
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
      <el-button size="mini" @click="addpage">添加版面</el-button>
      <el-button size="mini" @click="Preservation">保存</el-button>
      <el-button size="mini" @click="Reset">重置</el-button>
      <el-button size="mini" @click="cancel">取消</el-button>
    </div>

    <!-- 版面 -->
    <div class="space" v-for="(item,index) in Page" :key="index">
      
      <!-- 版面标题、添加移动box、删除版面 -->
      <div style="text-align: left;text-indent: 20px;margin: 10px 0;">
        <span>版面标题：</span>
        <el-input
          style="width: 200px;margin-right:30px"
          size="mini"
          v-model="item.title"
          placeholder="请输入版面标题"
        ></el-input>
        <el-button size="mini" @click="delpage(index)">删除版面</el-button>
        <el-button size="mini" @click="addbox(index)">导入信息</el-button>
      </div>

      <!-- 移动区域添加背景图 -->
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
          <el-button slot="trigger" size="mini" type="primary" @click="upimg(index)">选取文件</el-button>
        </el-upload>
      </div>

      <!-- 移动区域 -->
      <div :id="'father'+index" class="father" :style="{backgroundImage:'url(' + item.image + ')'}">
        <div
          :id="'box'+index+index1"
          :style="{width: item1.width+'px' , height: item1.height+'px' }"
          class="box"
          @click="dianjibox(index,index1)"
          v-for="(item1,index1) in item.form"
          :key="index1"
        >
          <div :id="'scaledel'+index+index1" class="scaledel">×</div>
          <div class="txtcontent">{{item1}}</div>
          <div :id="'scale'+index+index1" class="scalex"></div>
        </div>
      </div>
      <!--  -->
    </div>
  </div>
</template>
<script>
import { Message } from "element-ui";
export default {
  data: function() {
    return {
      Page: [
        {
          title: "",
          image: "",
          form: [
            { name: "第一个", width: 100, height: 100, x: 0, y: 0 },
            { name: "第二个", width: 100, height: 100, x: 0, y: 0 }
          ]
        }
      ],

      // 标题时间
      Newsform: {
        title: "",
        time: ""
      },

      uploadid: 0, //上传图片时的index
      fileList: [] //上传图片储存
    };
  },
  methods: {

    // 添加版面
    addpage(){
      let frame = {
        title: "",
        image: "",
        form: []
      };
      this.Page.push(frame);
    },

    // 删除版面
    delpage(index) {
      this.Page.splice(index, 1);
    },

    // 添加box
    addbox(index) {
      let obj = {
        name: "第" + 2 + "个",
        width: 100,
        height: 100,
        x: 0,
        y: 0
      };
      this.Page[index].form.push(obj);
      console.log(this.Page, "所有的数九");
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
    upimg(index) {
      console.log(index, "第几个版面的index");
      this.uploadid = index;
      console.log(this.uploadid, "版面的index值");
    },

    // 保存
    Preservation(){
      console.log(this.Page,"保存时的版面数据")
      Message.success("保存成功");
    },

    // 重置
    Reset(){
      this.Page = []
      let frame = {
        title: "",
        image: "",
        form: []
      };
      this.Page.push(frame)
       Message.success("重置成功");
    },

    // 取消
    cancel(){
      Message.success("取消了");
    },

    // 点击box拖动
    dianjibox(index, index1) {
      const _this = this;
      // box是装图片的容器,fa是图片移动缩放的范围,scale是控制缩放的小图标,scaledel是删除移动box
      var box = document.getElementById("box" + index + index1);
      var fa = document.getElementById("father" + index);
      var scale = document.getElementById("scale" + index + index1);
      var scaledel = document.getElementById("scaledel" + index + index1);

      // box移动
      box.onmousedown = function(ev) {
        console.log(ev, "点击时传的值");
        var oEvent = ev;
        // 浏览器有一些图片的默认事件,这里要阻止
        oEvent.preventDefault();
        var disX = oEvent.clientX - box.offsetLeft;
        var disY = oEvent.clientY - box.offsetTop;

        fa.onmousemove = function(ev) {
          console.log(ev, "移动时传的值");
          oEvent = ev;
          oEvent.preventDefault();

          if (oEvent.layerX < 0) {
            var x = box.offsetLeft;
          } else if (oEvent.layerY < 0) {
            var y = box.offsetTop;
          } else {
            var x = oEvent.clientX - disX;
            var y = oEvent.clientY - disY;
          }

          console.log(x, y, "坐标");

          // 图形移动的边界判断
          x = x <= 0 ? 0 : x;
          x =
            x >= fa.offsetWidth - box.offsetWidth
              ? fa.offsetWidth - box.offsetWidth
              : x;
          y = y <= 0 ? 0 : y;
          y =
            y >= fa.offsetHeight - box.offsetHeight
              ? fa.offsetHeight - box.offsetHeight
              : y;
          box.style.left = x + "px";
          box.style.top = y + "px";
          console.log(x, y, "最终的坐标");
          _this.Page[index].form[index1].x = x;
          _this.Page[index].form[index1].y = y;
        };
        // 图形移出父盒子取消移动事件,防止移动过快触发鼠标移出事件,导致鼠标弹起事件失效
        fa.onmouseleave = function() {
          fa.onmousemove = null;
          fa.onmouseup = null;
        };
        // 鼠标弹起后停止移动
        fa.onmouseup = function() {
          fa.onmousemove = null;
          fa.onmouseup = null;
        };
      };

      // 图片缩放效果
      scale.onmousedown = function(e) {
        // 阻止冒泡,避免缩放时触发移动事件
        e.stopPropagation();
        e.preventDefault();
        var pos = {
          w: box.offsetWidth,
          h: box.offsetHeight,
          x: e.clientX,
          y: e.clientY
        };
        fa.onmousemove = function(ev) {
          ev.preventDefault();
          // 设置图片的最小缩放为20*20
          var w = Math.max(20, ev.clientX - pos.x + pos.w);
          var h = Math.max(20, ev.clientY - pos.y + pos.h);

          // 设置图片的最大宽高
          w =
            w >= fa.offsetWidth - box.offsetLeft
              ? fa.offsetWidth - box.offsetLeft
              : w;
          h =
            h >= fa.offsetHeight - box.offsetTop
              ? fa.offsetHeight - box.offsetTop
              : h;
          box.style.width = w + "px";
          box.style.height = h + "px";
          console.log(w, h, "最终宽高");
          _this.Page[index].form[index1].width = w;
          _this.Page[index].form[index1].height = h;
        };
        fa.onmouseleave = function() {
          fa.onmousemove = null;
          fa.onmouseup = null;
        };
        fa.onmouseup = function() {
          fa.onmousemove = null;
          fa.onmouseup = null;
        };
      };

      // box删除效果
      scaledel.onmousedown = function(e) {
        e.stopPropagation();
        e.preventDefault();
        _this.Page[index].form.splice(index1, 1);
        fa.onmouseleave = function() {
          fa.onmousemove = null;
          fa.onmouseup = null;
        };
        fa.onmouseup = function() {
          fa.onmousemove = null;
          fa.onmouseup = null;
        };
      };
    }
  },
  mounted() {}
};
</script>
<style scoped>
/* 版面 */
.space {
  border: 1px solid red;
}

/* 移动的盒子 */
.box {
  /* width: 100px;
  height: 100px; */
  background-color: rgba(127, 255, 212, 0.8);
  position: absolute;
  cursor: move;
  overflow: hidden;
}

/* 移动区域 */
.father {
  height: 622px;
  width: 421px;
  border: 1px solid #cccccc;
  position: relative;
}

/* 文字区域大小 */
.txtcontent {
  width: 100%;
  min-height: 20px;
  overflow: hidden;
  background-color: rgba(255, 255, 255, 0.8);
  font-size: 12px;
  font-weight: bold;
}

/* 缩放图片按钮 */
.scalex {
  width: 10px;
  height: 10px;
  overflow: hidden;
  cursor: se-resize;
  position: absolute;
  right: 0;
  bottom: 0;
  background-color: rgb(122, 191, 238);
}

/* 删除图片按钮 */
.scaledel {
  width: 10px;
  height: 10px;
  overflow: hidden;
  cursor: pointer;
  position: absolute;
  right: 0;
  top: 0;
  background-color: rgb(122, 191, 238);
  line-height: 10px;
}
</style>