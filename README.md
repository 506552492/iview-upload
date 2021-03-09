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

| 属性       | 说明                                                                                                      |           类型            |  默认  | 版本  |
| :--------- | --------------------------------------------------------------------------------------------------------- | :-----------------------: | :----: | :---: |
| filterable | 是否支持搜索                                                                                              |          Boolean          | false  | 1.0.6 |
| clearable  | 是否可以清空选项，只在单选时有效                                                                          |          Boolean          | false  | 1.0.6 |
| treeData   | 下拉树的数据                                                                                              |           Array           |   空   | 1.0.6 |
| multiple   | 是否允许多选                                                                                              |          Boolean          | false  | 1.0.6 |
| value      | 指定选中项目的 value 值，可以使用 v-model 双向绑定数据。单选时只接受 String 或 Number，多选时只接受 Array | String 或 Number 或 Array |   空   | 1.0.6 |
| showQuery  | 是否展示下拉的搜索框                                                                                      |           true            | 1.0.8  |
| disabled   | 是否禁用或启用当前的下拉框                                                                                |           true            | 1.0.10 |

# 方法说明

| 方法             | 说明                       |     返回值      |
| :--------------- | -------------------------- | :-------------: |
| on-select-change | 下拉框选中的时候触发的事件 | hideVal,showVal |

```


```
