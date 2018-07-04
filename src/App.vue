<template>
  <div id="app">
    <PictureUploadQn
      equipment="pc"
      :width="100"
      :borderRadius="5"
      :tokenUrl="tokenUrl"
      :url="url"
      :domain="domain"
      :maxNum="maxNum"
      :maxSize="maxSize"
      :allowType="allowType"
      @finishUploadAll="finishUploadAll"
    ></PictureUploadQn>
  </div>
</template>

<script>
  import PictureUploadQn from './lib/picture-upload-qn';
  export default {
    name: 'app',
    data () {
      return {
        url: 'http://upload.qiniu.com/',
        domain: '//pb10jnkff.bkt.clouddn.com',
        tokenUrl: 'http://heikaimu.top:4000/qiniu/token?bucket=test',
        maxNum: 4,
        maxSize: 1024 * 1024 * 1,
        allowType: 'jpeg/jpg/png'
      }
    },
    methods: {
      finishUploadAll(res) {
        console.log(res);
      }
    },
    components: {
      PictureUploadQn
    }
  }
</script>

<style lang="scss">
</style>
