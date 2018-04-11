<template>
<div class="upload-wrapper">
<span v-for="(item,index) in srcArr" :key="index" class="img-outer"><img :src="item" class="upload-img" @click="bigImg(index)"/><span class="cancel-wrapper">
<span class="cancel" @click.stop="cancelImg(index)">取消</span></span></span>
<span class="input-wrapper">
<input type="file" @change='chooseChange($event)' accept="image/*" id="file" />
<span class="input-mask">+</span>
</span>
<div class="alert" v-if="alertShow">
<span class="alert-msg">{{alertMsg}}</span>
<button @click="msgConfirm">确定</button>
</div>
</div>
</template>
<style>
.upload-wrapper{
display: inline-block;
}
#file{
height:78px;
width:88px;
outline: none;
vertical-align: top;
opacity: 0;
filter:alpha(opacity=0);
position: absolute;
left:0;
top:0;
z-index:999;
}
.upload-img{
width:90px;
height:80px;
}
.img-outer{
margin-right:10px;
font-size:0;
display: inline-block;
position:relative;
width:90px;
height:80px;
}
.cancel-wrapper{
height:100%;
width:100%;
display: inline-block;
background:rgba(0,0,0,0.3);
position:absolute;
top:0;
left:0;
display: none;
}
.upload-wrapper .cancel{
position:absolute;
top:10px;
right:5px;
font-size:14px;
z-index:10000;
color:#fff;
cursor: pointer;
}
.img-outer:hover .cancel-wrapper{
display: block;
}
.alert{
padding:20px 30px;
background:#000;
position:fixed;
top:50%;
left:50%;
transform:translate3d(-50%,-50%,0);
background:#000;
z-index: 999;
}
.alert-msg{
margin-bottom:30px;
display: block;
}
.alert button{
background:blue;
color:#fff;
padding:10px;
border:none;
border-radius:3px;
}
.input-wrapper{
position: relative;
display: inline-block;
width:90px;
height:80px;
box-sizing:border-box;
border:1px solid #ccc;
}
.input-mask{
width:100%;
height:100%;
position:absolute;
left:0;
top:0;
text-align:center;
font-size:50px;
line-height:68px;
display: inline-block;
background:#F3FBFA;
color:#1AB394;
cursor: pointer;
}
</style>
<script>
export default {
    props: {
        maxLength: {
            default: 5,
            type: Number
        }
    },
    data () {
        return {
            imgArr: [], // 存的file文件
            srcArr: [], // 存的图片预览地址
            alertMsg: '', // 图片格式大小等提示信息
            alertShow: false
        };
    },
    methods: {
        chooseChange (evt) {
            let getFile = evt.target.files[0];
            let maxSize = 1024 * 1024;
            let len = this.imgArr.length;
            if (getFile.size < maxSize && len <= this.maxLength) {
                let src = URL.createObjectURL(getFile);
                this.srcArr.push(src);
                this.imgArr.push(getFile);
            } else {
                if (getFile.size > maxSize) {
                    this.alertState('最大1M');
                } else if (len > this.maxLength) {
                    this.alertState(`最多${this.maxLength}张`);
                }
            }
        },
        getFormData (arr) {
            let data = new FormData();
            let len = arr.length;
            for (let i = 0;i < len;i++) {
                data.append('file' + i, arr[i]);
            }
            return data;
        },
        bigImg (index) {
            console.log(index);
            this.bigShow = index;
        },
        smallImg (index) {
            this.bigShow = index;
        },
        msgConfirm () {
            this.alertShow = false;
        },
        cancelImg (index) {
            this.srcArr.splice(index, 1);
            this.imgArr.splice(index, 1);
        },
        alertState (info) {
            this.alertShow = true;
            this.alertMsg = info;
            setTimeout(() => {
                this.alertShow = false;
            }, 2000);
        }
    },
    watch: {
        imgArr () {
            let data = new FormData();
            let len = this.imgArr.length;
            for (let i = 0;i < len;i++) {
                data.append('file', this.imgArr[i]);
            }
            if (len > 0) {
                this.$emit('formData', data); // 将上传图片数据传递给父元素用于ajax提交
            } else {
                return false;
            }
        }
    }
};
</script>
