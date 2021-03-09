# iview-upload

> 基于 iview 进行二次开发的图片上传组件，支持上传后本地大图回显、私密大图回显不显示具体图片、小图回显、上传前压缩、集成 viewjs 全屏预览插件（注：使用前先引入 iview，否则无法使用）

# 安装

```js
npm install iview-upload --save
```

# 引入

```js
import uploadCustom from "iview-upload";
```

在引入的 vue 模块中需要在 components 上注册这个组件，这样才可以正常使用这个组件：

```js
export default {
  name: 'app',
  components:{
    uploadCustom
  },
  data () {
     return {
        ...
     }
  },
  ...
}
```

# 效果

## 多选效果

# 调用方式

```js
<upload-custom v-model="val"></upload-custom>
```

# 参数说明

| 属性                    | 说明                                                  |  类型   |      默认       | 版本  |
| :---------------------- | ----------------------------------------------------- | :-----: | :-------------: | :---: |
| multiple                | 是否支持多张图片                                      | Boolean |      false      | 1.0.0 |
| action 上传的地址，必填 |                                                       | String  |       空        | 1.0.0 |
| headers                 | 设置上传的请求头部                                    | Object  |        -        | 1.0.0 |
| fileData                | 初始图片数据                                          | String  |       空        | 1.0.0 |
| params                  | 上传时附带的额外参数                                  | Object  |        -        | 1.0.0 |
| readonly                | 是否只读                                              | Object  |      false      | 1.0.0 |
| noShow                  | 隐私图片不展示具体                                    | Object  |      false      | 1.0.0 |
| maxSize                 | 文件大小限制，单位 byte                               | Number  | 2 _ 1024 _ 1024 | 1.0.0 |
| format                  | 支持的文件类型,识别文件的后缀名                       |  Array  |       []        |
| autoCompress            | 支持上传前自动压缩                                    | Boolean |      false      |
| maxWidth                | 图片最大宽度，超过则压缩，autoCompress 为 true 时生效 | Number  |      2000       |
| maxHeight               | 图片最大高度，超过则压缩，autoCompress 为 true 时生效 | Number  |      2000       |

# 方法说明

| 方法        | 说明           |     返回值     |
| :---------- | -------------- | :------------: |
| on-success  | 上传成功       | response, file |
| on-remove   | 文件移除       |                |
| on-progress | 文件上传时返回 |      file      |

```


```
