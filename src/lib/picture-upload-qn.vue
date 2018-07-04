<template>
  <div>
    <div class="picture-upload-qn-wrapper">
      <ul class="photos-wrapper">
        <li class="photos"
            :style="{'width':`${width}px`, 'height':`${width}px`, 'borderRadius':`${borderRadius}px`}"
            v-for="(item,index) in picList"
            @mouseover="showIconIndex=index"
            @mouseout="showIconIndex=-1"
        >
          <div class="img" :class="{'blur': !item.load}" :style="{'background-image': `url(${item.src})`}"></div>
          <div class="progress"><i class="line" :style="{width: `${item.progress}%`}" v-if="!item.load"></i></div>
          <template v-if="equipment==='pc'">
            <div class="right-icon" v-show="item.load && showIconIndex!==index"></div>
            <div class="btn-wrapper" :class="{'active': showIconIndex===index}">
              <span class="icon big" @click="handleCheck(index)"></span>
              <span class="icon del" @click="handleDel(index)"></span>
            </div>
          </template>
          <template v-if="equipment==='mobile'">
            <div class="mobile-btn-wrapper">
              <span class="icon big" @click="handleCheck(index)"></span>
              <span class="icon del" @click="handleDel(index)"></span>
            </div>
          </template>
        </li>
        <li class="upload-btn"
            :style="{'width':`${width}px`, 'height':`${width}px`, 'borderRadius':`${borderRadius}px`}"
            v-if="picList.length<maxNum">
          <label for="upload" class="btn" @change="selectFile">
            <input type="file" multiple="multiple" id="upload" v-show="false" ref="file">
          </label>
        </li>
      </ul>
      <div class="error-msg" v-show="errorMsg!==''">{{errorMsg}}</div>
    </div>
    <PictureCheck
      v-if="showPicCheck"
      :list="picList"
      :index="clickIndex"
      :equipment="equipment"
      @closeCheck="showPicCheck=false"
    ></PictureCheck>
  </div>
</template>

<script>
  import PictureCheck from './picture-check.vue';
export default {
  props: {
    equipment: {
      default: 'pc'
    },
    width: {
      default: 100
    },
    borderRadius: {
      default: 100
    },
    // 最大上传数
    maxNum: {
      default: 3
    },
    // 图片大小
    maxSize: {
      default: 1024 * 1024
    },
    // 图片大小
    allowType: {
      default: 'jpeg/jpg/png'
    },
    // 七牛的上传url
    url: {
      default: '',
    },
    // 图片回显的外网地址
    domain: {
      default: ''
    },
    // 获取签名的地址
    tokenUrl: {
      default: ''
    }
  },
  data () {
    return {
      picList: [],
      showIconIndex: -1, // 按钮层索引
      errorMsg: '',
      singleNum: 0, // 单次上传的总数
      currentNum: 0, // 当前上传成功数
      token: '', // 签名
      showPicCheck: false,
      clickIndex: 0
    }
  },
  created() {
    console.log(this.equipment);
  },
  methods: {
    // 选择文件
    async selectFile() {
      // 获取token
      const { token } = await this.getToken();
      this.token = token;
      this.errorMsg = '';
      // 选择的所有文件，选择上传的文件的数量，获取已经上传了的文件数量作为本次上传文件开始的索引
      const files = this.$refs.file.files;
      let cl = this.picList.length;
      let index = cl;
      // 将本次选择的文件组合成数组集合
      let selectFiles = [];
      for (let i in files) {
        const file = files[i];
        if (file.size) {
          selectFiles.push(file);
        }
      }
      // 判断上传的文件总数是否超过限定，如果没有则开始上传
      this.singleNum = selectFiles.length;
      this.chargeFiles(this.singleNum + cl, selectFiles, () => {
        selectFiles.forEach((item) => {
          const reader = new FileReader();
          reader.readAsDataURL(item)
          reader.onload = (e) => {
            const preview = e.currentTarget.result;
            this.picList.push({
              load: false,
              src: preview,
              index: index,
              progress: 0
            });
            this.fileUpload(item, index);
            index += 1; // 索引加1
          }
        })
      });
    },
    // 判断文件的类型和总数量
    chargeFiles(l, files, cb) {
      let errorSizeNum = 0;
      let errorTypeNum = 0;
      if (l > this.maxNum) {
        this.errorMsg = '文件数量过多';
        return false;
      } else {
        files.forEach((item) => {
          const size = item.size;
          const type = item.type.split('/')[1];
          if (size > this.maxSize) {
            errorSizeNum += 1;
          }
          if (this.allowType.indexOf(type) === -1) {
            errorTypeNum += 1;
          }
        })
        const sizeUnit = this.maxSize / (1024 * 1024) >= 1 ? this.maxSize / (1024 * 1024) + 'M' : this.maxSize / 1024 + 'KB';
        const sizeMsg = `文件大小不符合规定，不能大于${sizeUnit}`;
        const typeMsg = `文件类型不符合规定，允许类型：${this.allowType}`;
        if (errorSizeNum > 0 && errorTypeNum === 0) {
          this.errorMsg = sizeMsg;
        } else if (errorSizeNum === 0 && errorTypeNum > 0) {
          this.errorMsg = typeMsg;
        } else if (errorSizeNum > 0 && errorTypeNum > 0) {
          this.errorMsg = `${sizeMsg}；${typeMsg}`;
        } else {
          cb();
        }
      }
    },
    // 获取token签名
    getToken() {
      const xhr = new XMLHttpRequest();
      return new Promise((resolve, reject) => {
        xhr.onreadystatechange = () => {
          if (xhr.readyState === 4) {
            if (xhr.status === 200) {
              resolve(JSON.parse(xhr.responseText));
            } else {
              reject(request.status);
            }
          }
        }
        xhr.open('GET', this.tokenUrl, true);
        xhr.send();
      })
    },
    // 上传文件
    fileUpload(file, index) {
      const formData = new FormData();
      formData.append('file', file);
      formData.append('token', this.token);
      const xhr = new XMLHttpRequest();
      xhr.open('POST', this.url, true);
      // 监听上传
      xhr.onreadystatechange = () => {
        if (xhr.readyState === 4) {
          if (xhr.status === 200) {
            const res = JSON.parse(xhr.responseText);
            this.picList[index].link = `${this.domain}/${res.hash}`;
            this.picList[index].load = true;
            this.currentNum += 1;
            if (this.currentNum === this.singleNum) {
              this.currentNum = 0;
              this.finishUploadAll();
            }
          } else {
            console.log(xhr.status);
          }
        }
      }
      // 上传进度
      xhr.upload.onprogress = (evt) => {
        if (evt.lengthComputable) {
          this.picList[index].progress = parseInt(Math.round(evt.loaded * 100 / evt.total));
        }
      }
      // 发送
      xhr.send(formData);
    },
    // 删除图片
    handleDel(index) {
      this.picList.splice(index, 1);
      this.finishUploadAll();
    },
    // 对外的接口,输出最新的图片地址集合
    finishUploadAll() {
      const uploadUrl = this.picList.map((item) => {
        return item
      })
      this.$emit('finishUploadAll', uploadUrl);
    },
    // 查看图片
    handleCheck(index) {
      this.clickIndex = index;
      this.showPicCheck = true;
    }
  },
  components: {
    PictureCheck
  }
}
</script>

<style lang="scss">
  *{
    margin: 0;
    padding: 0;
  }
  li{
    list-style: none;
  }
  .picture-upload-qn-wrapper{
    background: #fff;
    padding: 10px 10px 0 10px;
    border-bottom: 1px solid #f2f2f2;
    .photos-wrapper{
      display: flex;
      flex-wrap: wrap;
      .photos{
        margin-right: 10px;
        margin-bottom: 10px;
        position: relative;
        background: #f2f2f2;
        border: 1px solid #e7e7e7;
        overflow: hidden;
        .img{
          position: absolute;
          left: 0;
          right: 0;
          bottom: 0;
          top: 0;
          z-index: 3;
          width: 100%;
          height: 100%;
          transition: 0.2s;
          background-position: center center;
          background-size: cover;
          background-repeat: no-repeat;
          &.blur{
            -webkit-filter: blur(2px);
            filter: blur(2px);
          }
        }
        .progress{
          position: absolute;
          left: 10px;
          right: 10px;
          bottom: 10px;
          z-index: 4;
          border-radius: 2px;
          background: rgba(0,0,0,0.3);
          overflow: hidden;
          .line{
            display: block;
            height: 4px;
            background: #5cb85c;
          }
        }
        .right-icon{
          display: block;
          width: 50px;
          height: 25px;
          background: #0FCE62;
          position: absolute;
          right: -17px;
          top: -4px;
          z-index: 5;
          transform: rotate(45deg);
          &:after{
            content: '';
            width: 16px;
            height: 16px;
            background: url("./right.png") no-repeat center center / cover;
            position: absolute;
            left: 18px;
            top: 9px;
            transform: rotate(-45deg);
          }
        }
        .btn-wrapper{
          position: absolute;
          left: 0;
          right: 0;
          bottom: 0;
          top: 0;
          z-index: 4;
          background: rgba(0,0,0,0.3);
          transition: .1s;
          transform: scale(0.8);
          opacity: 0;
          display: flex;
          align-items: center;
          justify-content: center;
          &.active{
            transform: scale(1);
            opacity: 1;
          }
          .icon{
            width: 20px;
            height: 20px;
            margin: 0 10px;
            &.big{
              background: url("./check.png") no-repeat center center;
            }
            &.del{
              background: url("./del.png") no-repeat center center;
            }
          }
        }
        .mobile-btn-wrapper{
          position: absolute;
          left: 0;
          right: 0;
          bottom: 0;
          top: 0;
          z-index: 4;
          .big{
            position: absolute;
            left: 0;
            right: 0;
            bottom: 0;
            top: 0;
            z-index: 5;
          }
          .del{
            width: 20px;
            height: 20px;
            position: absolute;
            right: 2px;
            top: 2px;
            z-index: 5;
            border-radius: 50%;
            border: 1px solid #f2f2f2;
            background: url("./close.png") red no-repeat center center / 60%;
          }
        }
      }
      .upload-btn{
        background: #fff;
        border: 1px dashed #cdcdcd;
        display: flex;
        justify-content: center;
        align-items: center;
        margin-bottom: 10px;
        .btn{
          display: block;
          width: 60px;
          height: 60px;
          background: url("./add.png") no-repeat center center;
        }
      }
    }
    .error-msg{
      margin-top: 10px;
      padding: 5px 10px;
      border-radius: 2px;
      background-color: #fef0f0;
      color: #f56c6c;
      font-size: 12px;
    }
  }
</style>
