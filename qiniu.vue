<template>
    <input name="file" type="file" ref="qiniu" @change="updata" class="qiniuUpdata"/>
</template>

<script>
    export default {
        name: "qiniu.vue",
        data(){
            return{
                token:""
            }
        },
        mounted(){
            this.gettoken()
        },
        props:{
            url:{
                type:String,
                default:""
            },
            //上传到七牛的文件名前缀，方便在七牛查找
            prefix:{
                type: String,
                default: 'cs_'
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
                        this.token=data.token;
                    })
            },
            //选择图片并上传
            updata:function () {
                let filetype=this.$refs.qiniu.files[0].type;//获取文件类型
                if(filetype!='image/gif' && filetype!='image/jpeg' && filetype!='image/png'){//判断是否图片类型
                    alert("请上传图片类型文件");
                    return false
                }
                let SuffixName="";//文件后缀名
                switch (filetype) {
                    case 'image/gif' :
                        SuffixName = '.gif';
                        break;
                    case 'image/jpeg' :
                        SuffixName = '.jpg';
                        break;
                    case 'image/gif' :
                        SuffixName = '.png';
                        break
                }
                var formData = new FormData();
                formData.append("key", this.prefix+Date.parse(new Date())+SuffixName);
                formData.append("file", this.$refs.qiniu.files[0]);
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
                axios.post('https://upload.qiniup.com/',formData)
                    .then(data=>{
                        this.$emit('ok',data);
                        this.$refs.qiniu.value = null;
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
    input.qiniuUpdata{ width: 100%; height: 100%; opacity: 0}
</style>