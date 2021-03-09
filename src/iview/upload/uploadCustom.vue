<template>
  <div>
    <Upload
      :data="params"
      :show-upload-list="showUploadList"
      :on-success="uploadSuccess"
      :format="format"
      :max-size="maxSize"
      :multiple="multiple"
      :beforeUpload="beforeUpload"
      :onProgress="onProgress"
      :on-error="uploadError"
      :headers="headers"
      :on-format-error="formatError"
      :on-exceeded-size="appResSize"
      type="drag"
      :action="action"
      :disabled="readonly"
      class="myUpload"
    >
      <div class="_img">
        <div class="upload-large" v-if="img || showProgress">
          <template v-if="!showProgress">
            <checkImg
              :src="img"
              style="width: 100%; max-width: 100%"
            ></checkImg>
          </template>
          <template v-else="">
            <Progress
              style="line-height: 130px"
              :percent="percentage"
              hide-info
            ></Progress>
          </template>
        </div>
        <div v-else="" class="defaultBg">+</div>
        <div v-if="noShowImg" class="allMask">已上传</div>
        <div v-if="!readonly" class="mask">+</div>
      </div>
    </Upload>
  </div>
</template>

<script>
import checkImg from './checkImg.vue'
export default {
  props: {
    showUploadList: {
      type: Boolean,
      default: false
    },
    multiple: {
      type: Boolean,
      default: false
    },
    action: {
      type: String,
      default: function () {
        return '';
      }
    },
    headers: {
      type: Object,
      default: function () {
        return {};
      }
    },
    fileData: String,
    params: {
      type: Object,
      default: function () {
        return {};
      }
    },
    readonly: {
      type: Boolean,
      default: false
    },
    noShow: {
      type: Boolean,
      default: false
    },
    maxSize: {
      type: Number,
      default: function () {
        return 2 * 1024 * 1024
      }
    },
    format: {
      type: Array,
      default () {
        return ['jpg', 'jpeg', 'png', 'bmp', 'gif'];
      }
    },
    //是否自动压缩到2M以内
    autoCompress: {
      type: Boolean,
      default: false
    },
    maxWidth: {
      type: Number,
      default: function () {
        return 2000
      }
    },
    maxHeight: {
      type: Number,
      default: function () {
        return 2000
      }
    }
  },
  components: {
    //组件
    checkImg
  },
  data () {
    //数据
    return {
      showProgress: false,
      noShowImg: this.noShow,
      img: this.fileData
    };
  },
  mounted () {
    //初始化
  },
  computed: {
    //实时计算
  },
  watch: {
    //实时监听
    fileData (val) {
      this.img = val;
    },
    noShow (val) {
      this.noShowImg = val;
    }
  },
  methods: {
    //方法
    uploadSuccess (res, file) {
      this.showProgress = false;
      this.$emit('on-success', res, file);
      if (this.noShowImg) {
        this.img = '';
        this.noShowImg = false;
      }
    },
    remove () {
      this.$emit('on-remove');
    },
    uploadError () {
      this.showProgress = false;
      this.$Message.error('上传失败');
    },
    formatError () {
      this.$Message.error('请上传正确图片格式');
    },
    appResSize () {
      this.$Message.error('图片大小不可超过10M');
    },
    onProgress (e, file) {
      this.showProgress = true;
      this.percentage = file.percentage;
      this.$emit('on-progress', file);
    },
    beforeUpload (file) {
      if (this.autoCompress) {
        return new Promise((resolve) => {
          this.compressImageByWH(file, function (newFile) {
            resolve(newFile);
          })

        });
      }
    },
    /**
     * js压缩图片
     * @param file 图片文件对象
     * @param quality 压缩倍率 0~1
     * @constructor
     */
    compressImageByquality (file, callback) {
      // 图片小于maxSize不压缩
      if (file.size < this.maxSize) {
        callback(file);
      }

      let quality = (file.size / this.maxSize).toFixed(2);

      //保存文件名，后边用到
      const name = file.name;
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = function (e) {
        const src = e.target.result;

        const img = new Image();
        img.src = src;
        img.onload = function (e) {
          const w = img.width;
          const h = img.height;
          //生成canvas
          const canvas = document.createElement('canvas');
          const ctx = canvas.getContext('2d');
          // 创建属性节点
          const anw = document.createAttribute("width");
          anw.nodeValue = w;
          const anh = document.createAttribute("height");
          anh.nodeValue = h;
          canvas.setAttributeNode(anw);
          canvas.setAttributeNode(anh);

          //铺底色 PNG转JPEG时透明区域会变黑色
          ctx.fillStyle = "#fff";
          ctx.fillRect(0, 0, w, h);

          ctx.drawImage(img, 0, 0, w, h);
          // quality值越小，所绘制出的图像越模糊
          const base64 = canvas.toDataURL('image/jpeg', quality); //图片格式jpeg或webp可以选0-1质量区间

          // 返回base64转blob的值
          //						console.log('\u539F\u56FE' + (src.length / 1024).toFixed(2) + 'kb', '\u65B0\u56FE' + (base64.length / 1024).toFixed(2) + 'kb');
          //去掉url的头，并转换为byte
          const bytes = window.atob(base64.split(',')[1]);
          //处理异常,将ascii码小于0的转换为大于0
          const ab = new ArrayBuffer(bytes.length);
          const ia = new Uint8Array(ab);
          for (let i = 0; i < bytes.length; i++) {
            ia[i] = bytes.charCodeAt(i);
          }
          //通过Blob生成新图片文件对象
          let newFile = new Blob([ab], { type: 'image/jpeg' });
          //这里需要重新设置文件名
          newFile.name = name;
          let lastFile = new File([newFile], name, { type: 'image/jpeg', lastModified: Date.now() });
          callback(lastFile);

        };
        img.onerror = function (e) {
          console.error(e);
        };
      };
      reader.onerror = function (e) {
        console.error(e);
      }
    },
    compressImageByWH (file, callback) {
      // 图片小于maxSize不压缩
      let self = this;
      if (file.size < this.maxSize) {
        callback(file);
      }

      //保存文件名，后边用到
      const name = file.name;
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = function (e) {
        const src = e.target.result;

        const img = new Image();
        img.src = src;
        img.onload = function (e) {
          const originWidth = img.width;
          const originHeight = img.height;
          const maxWidth = this.maxWidth;
          const maxHeight = this.maxHeight;
          let targetWidth = originWidth;
          let targetHeight = originHeight;
          // 图片尺寸超过400x400的限制
          if (originWidth > maxWidth || originHeight > maxHeight) {
            if (originWidth / originHeight > maxWidth / maxHeight) {
              // 更宽，按照宽度限定尺寸
              targetWidth = maxWidth;
              targetHeight = Math.round(maxWidth * (originHeight / originWidth));
            } else {
              targetHeight = maxHeight;
              targetWidth = Math.round(maxHeight * (originWidth / originHeight));
            }
          }
          const canvas = document.createElement('canvas');
          const context = canvas.getContext('2d');
          // canvas对图片进行缩放
          canvas.width = targetWidth;
          canvas.height = targetHeight;
          // 清除画布
          context.clearRect(0, 0, targetWidth, targetHeight);
          // 图片压缩
          context.drawImage(img, 0, 0, targetWidth, targetHeight);
          /*第一个参数是创建的img对象；第二个参数是左上角坐标，后面两个是画布区域宽高*/
          //压缩后的图片base64 url
          /*canvas.toDataURL(mimeType, qualityArgument),mimeType 默认值是'image/jpeg';
           * qualityArgument表示导出的图片质量，只要导出为jpg和webp格式的时候此参数才有效果，默认值是0.92*/
          const base64 = canvas.toDataURL('image/jpeg', 0.92);//base64 格式

          // 返回base64转blob的值
          //						console.log('\u539F\u56FE' + (src.length / 1024 / 1024).toFixed(2) + 'm', '\u65B0\u56FE' + (base64.length / 1024 / 1024).toFixed(2) + 'm');
          //去掉url的头，并转换为byte
          const bytes = window.atob(base64.split(',')[1]);
          //处理异常,将ascii码小于0的转换为大于0
          const ab = new ArrayBuffer(bytes.length);
          const ia = new Uint8Array(ab);
          for (let i = 0; i < bytes.length; i++) {
            ia[i] = bytes.charCodeAt(i);
          }
          //通过Blob生成新图片文件对象
          let newFile = new Blob([ab], { type: 'image/jpeg' });
          //这里需要重新设置文件名
          newFile.name = name;
          if (newFile.size > this.maxSize) {
            self.compressImageByquality(newFile, newFile)
          } else {
            let lastFile = new File([newFile], name, { type: 'image/jpeg', lastModified: Date.now() });
            callback(lastFile);
          }


        };
        img.onerror = function (e) {
          console.error(e);
        };
      };
      reader.onerror = function (e) {
        console.error(e);
      }
    }
  }
};
</script>
<style lang="less">
.upload-large {
  display: inline-block;
  width: 170px;
  height: 144px;
  text-align: center;
  line-height: 60px;
  border: 1px solid transparent;
  border-radius: 4px;
  overflow: hidden;
  background: #fff;
  position: relative;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2);
  margin-right: 4px;
}

.upload-large img {
  width: 100%;
  height: 100%;
}

.upload-large-cover {
  display: none;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.6);
}

.upload-large:hover .upload-large-cover {
  display: block;
}

.upload-large-cover i {
  color: #fff;
  font-size: 20px;
  cursor: pointer;
  margin: 0 2px;
}

.myUpload {
  display: inline-block;
  width: 170px;
  ._img {
    width: 170px;
    height: 144px;
    line-height: 54px;
    position: relative;
  }
  .defaultBg {
    position: absolute;
    top: 0;
    left: 0;
    width: 170px;
    height: 144px;
    background: rgba(101, 101, 101, 0.6);
    color: #ffffff;
    opacity: 1;
    font-size: 100px;
    line-height: 110px;
    text-align: center;
  }
  .mask {
    position: absolute;
    top: 0;
    left: 0;
    width: 170px;
    height: 144px;
    background: rgba(101, 101, 101, 0.6);
    color: #ffffff;
    opacity: 0;
    font-size: 100px;
    line-height: 110px;
    text-align: center;
  }
  .allMask {
    position: absolute;
    top: 0;
    left: 0;
    width: 170px;
    height: 144px;
    background: #a1a1a1;
    color: #ffffff;
    font-size: 30px;
    line-height: 140px;
    text-align: center;
  }
}

.myUpload:hover .mask {
  opacity: 1;
}
</style>
