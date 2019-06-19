# VUE-七牛上传图片
## 依赖了axios
请自行安装<br>
#### npm 安装
```
$ npm install axios
```
#### CND 引入
```
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```
## 使用方法
在需要的组件中引用       
```
import Qiqiu from '@/components/qiniu.vue'
```
模板文件
```
@url           获取七牛token的地址
@ok            上传完成的回调地址
@prefix        上传文件前缀方便在七牛查找 默认值cs_
@maxSize       上传文件最大数量 默认值3
@fileSize      上传单位文件大小 默认值1M
@multiple      是否允许上传多张图片 默认值false

重要 <div class="shangchuan"> 样式自己定义，重要属性position: relative 组件内部必须识别这个属性
<div class="shangchuan">
    上传图片
    <Qiqiu :url='"/api/index/index"' @ok="ok" :multiple="true" :fileSize="1" :maxSize="1"></Qiqiu>
</div>
```
