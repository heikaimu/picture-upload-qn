<template>
  <el-container>
    <el-header class="header">Vue图片预览组件实例展示</el-header>
    <el-main>
      <el-row class="example">
        <el-col :span="12">
          <div class="pic-container2">
            <PictureUploadQn
              :equipment="equipment"
              :width="width"
              :borderRadius="borderRadius"
              :tokenUrl="tokenUrl"
              :url="url"
              :domain="domain"
              :maxNum="maxNum"
              :maxSize="maxSize"
              :allowType="allowType"
              @finishUploadAll="finishUploadAll"
            ></PictureUploadQn>
          </div>
          <div class="link-wrapper" v-if="list.length!==0">
            <h3>上传成功的链接</h3>
            <p v-for="i in list">{{i.link}}</p>
          </div>
        </el-col>
        <el-col :span="12">
          <div class="form-container">
            <h3>配置参数</h3><br>
            <el-form ref="form" label-width="180px">
              <el-form-item label="上传地址">
                <el-input v-model="url"></el-input>
              </el-form-item>
              <el-form-item label="图片地址">
                <el-input v-model="domain"></el-input>
              </el-form-item>
              <el-form-item label="获取签名的api接口地址">
                <el-input v-model="tokenUrl"></el-input>
              </el-form-item>
              <el-form-item label="最大上传数量">
                <el-input-number style="width: 100%" v-model="maxNum" :min="1" :max="9" label="1-9"></el-input-number>
              </el-form-item>
              <el-form-item label="图片尺寸限制（B）">
                <el-input v-model="maxSize"></el-input>
              </el-form-item>
              <el-form-item label="图片类型">
                <el-input v-model="allowType"></el-input>
              </el-form-item>
              <el-form-item label="pc端还是移动端">
                <el-radio-group v-model="equipment">
                  <el-radio label="pc">pc</el-radio>
                  <el-radio label="mobile">移动</el-radio>
                </el-radio-group>
              </el-form-item>
              <el-form-item label="尺寸">
                <el-slider
                  v-model="width"
                  :min="40"
                  :max="200"
                  show-input>
                </el-slider>
              </el-form-item>
              <el-form-item label="圆角">
                <el-slider
                  v-model="borderRadius"
                  :max="150"
                  show-input>
                </el-slider>
              </el-form-item>
            </el-form>
          </div>
        </el-col>
      </el-row>
      <el-row class="link">
        <el-col :span="24">
          <a href="https://github.com/heikaimu/picture-upload-qn">github地址：https://github.com/heikaimu/picture-upload-qn</a>
        </el-col>
      </el-row>
    </el-main>
  </el-container>
</template>

<script>
  import PictureUploadQn from 'picture-upload-qn';
  export default {
    data () {
      return {
        url: 'http://upload.qiniu.com/',
        domain: 'http://pb10jnkff.bkt.clouddn.com',
        tokenUrl: 'http://heikaimu.top:4000/qiniu/token?bucket=test',
        maxNum: 4,
        maxSize: 1024 * 1024 * 1,
        allowType: 'jpeg/jpg/png',
        width: 100,
        borderRadius: 5,
        equipment: 'pc',
        list: []
      }
    },
    methods: {
      finishUploadAll(res) {
        this.list = res;
      }
    },
    components: {
      PictureUploadQn
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .header{
    background: #333;
    text-align: center;
    line-height: 60px;
    color: #fff;
  }
  .example{
    background: #fff;
  }
  .pic-container2{
    background: #fff;
  }
  .form-container{
    background: #f2f2f2;
    padding: 20px;
  }
  .link{
    padding-top: 30px;
    text-align: center;
  }
  .link a{
    color: #333;
  }
  .link-wrapper{
    text-align: left;
    padding-top: 100px;
  }
</style>
