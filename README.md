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
@url 获取七牛token的地址
@ok  上传完成的回调地址
<div class="shangchuan">
    <Qiqiu :url='"/api/index/index"' @ok="ok"></Qiqiu>
</div>
```
