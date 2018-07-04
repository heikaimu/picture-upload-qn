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

| 参数             | 描述                      | 可选值             | 默认值                            |
|------------------|--------------------------|--------------------|----------------------------------|
| equipment        | 用于切换pc和移动端的样式   | pc,mobile          | pc                               |      
| width            | 小图片的尺寸              |                    | 100                              |      
| borderRadius     | 小图片的圆角              |                    | 5                                |      
| tokenUrl         | 七牛签名接口，由后端提供   |                    |                                  |      
| url              | 七牛上传接口，由七牛提供   |                    | http://upload.qiniu.com/         |      
| domain           | 拼接在返回路径前面的地址   |                    |                                  |      
| maxNum           | 最大上传数                |                    | 9                                |      
| maxSize          | 最大上传的图片尺寸         |                    | 1024 x 1024                      |  
| allowType        | 图片类型                  |                    | jpeg/jpg/png                     |  
| finishUploadAll  | 图片上传完的派发事件       |                    |                                  |  
