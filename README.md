基于vue的七牛图片上传组件

[演示地址](https://heikaimu.github.io/picture-preview-example/code/dist/index.html#/)

### 安装
```
npm i picture-upload-qn -S
```

### 引入
test.vue
```
import PictureUploadQn from 'picture-upload-qn';
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
```

### 参数解释
equipment: 设备类型, 可选值为 pc，mobile；默认pc
width:     小图尺寸

| Feature          | Notes                                                  | Docs         |
|------------------|--------------------------------------------------------|--------------|
| autocomplete     |                                                        |   [Docs][24] |                         
