<template>
    <input name="file" type="file" ref="qiniu" @change="updata" class="qiniuUpdata" :multiple="multiple"/>
</template>

<script>
    export default {
        name: "qiniu.vue",
        data(){
            return{
                token:"",
                updataimg:[]
            }
        },
        mounted(){
            this.gettoken()
        },
        props:{
            //是否可以传多张图片
            multiple:{
                type:Boolean,
                default:false
            },
            url:{
                type:String,
                default:""
            },
            //上传到七牛的文件名前缀，方便在七牛查找
            prefix:{
                type: String,
                default: 'cs_'
            },
            //限制最大能上传多少张图片
            maxSize:{
                type: Number,
                default: 3
            },
            //限制每个文件大小 单位M
            fileSize:{
                type: Number,
                default: 1
            }
        },
        methods:{
            //请求token
            gettoken:function(){
                if(!this.url){
                    alert("请先配置获取TOKEN的地址")
                }
                axios.get(this.url)
                    .then(data=>{
                        if(!data.token){
                            alert("获取tonken失败")
                            return false
                        }
                        this.token=data.token;
                    })
            },
            //选择图片并上传
            updata:function () {
                let filedata=this.$refs.qiniu.files;
                let filedataLength=filedata.length;
                let upLength=0;
                //限制上传张数
                if(filedataLength>this.maxSize){
                    alert("一次最多上传3张图片");
                    return false
                }
                for (let i=0; i<filedataLength;i++){
                    //判断文件大小
                    if(filedata[i].size/1048576>this.fileSize){
                        alert("您的文件"+filedata[i].name+"文件大小超过了"+this.fileSize+"M"+"请上传小于"+this.fileSize+"M的文件")
                        return false
                    }
                    this.up(filedata[i],i);
                    upLength++;
                    if(upLength>=filedataLength){
                        this.$refs.qiniu.value = null;
                        this.$emit('ok',this.updataimg);
                    }
                }
            },
            //上传图片方法
            up:function (filedata,mark) {
                if(filedata.type!='image/gif' && filedata.type!='image/jpeg' && filedata.type!='image/png'){//判断是否图片类型
                    alert("请上传图片类型文件");
                }
                let SuffixName="";//文件后缀名
                switch (filedata.type) {
                    case 'image/gif' :
                        SuffixName = '.gif';
                        break;
                    case 'image/jpeg' :
                        SuffixName = '.jpg';
                        break;
                    case 'image/png' :
                        SuffixName = '.png';
                        break
                }
                var formData = new FormData();
                formData.append("key", this.prefix+Date.parse(new Date())+'_'+mark+SuffixName);
                formData.append("file", filedata);
                formData.append("token", this.token);
                // 华东	z0	服务器端上传：http(s)://up.qiniup.com
                // 客户端上传： http(s)://upload.qiniup.com
                // 华北	z1	服务器端上传：http(s)://up-z1.qiniup.com
                // 客户端上传：http(s)://upload-z1.qiniup.com
                // 华南	z2	服务器端上传：http(s)://up-z2.qiniup.com
                // 客户端上传：http(s)://upload-z2.qiniup.com
                // 北美	na0	服务器端上传：http(s)://up-na0.qiniup.com
                // 客户端上传：http(s)://upload-na0.qiniup.com
                // 东南亚	as0	服务器端上传：http(s)://up-as0.qiniup.com
                // 客户端上传：http(s)://upload-as0.qiniup.com
                axios.post('https://up.qiniup.com/',formData,{
                    //获取上传进度
                    onUploadProgress: function (e) {
                        // console.log(e.loaded / e.total);
                        // console.log(e)
                    }
                })
                    .then(data=>{
                        this.updataimg.push(data)
                        // this.$emit('ok',data);
                        // this.$refs.qiniu.value = null;
                    })
                    .catch(data=>{
                        this.$refs.qiniu.value = null;
                        this.$emit('ok',data);
                        alert("请确定存储区域，查看地址：https://developer.qiniu.com/kodo/manual/1671/region-endpoint，代码请自行修改")
                    })
            }
        }
    }
</script>

<style>
    input.qiniuUpdata{ width: 100%; height: 100%; opacity: 0; position: absolute; left: 0; top: 0}
</style>