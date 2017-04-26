<template>
    <div id="app">
        <div class="container">
            <label for="">地址:</label>
            <input type="text" placeholder="请输入地址" @focus="onFocus" v-model="addressValue" @input="onInput" @blur="onBlur">
            <ul v-if="listShowHide">
                <li v-for="(item,index) in addressList" @click="onListClick(item.text)">{{item.text}}{{item.area}}</li>
            </ul>
        </div>
    </div>
</template>

<script>
export default {
    data () {
        return {
            addressValue:'',
            listShowHide:false,
            addressList:[
            ]
        }
    },
    methods:{
        onFocus () {
            let that = this;
            if(this.addressValue!='') {
                let value = this.addressValue;
                let city = '北京市';
                let url = `http://api.map.baidu.com/place/v2/suggestion?query=${value}&region=${city}&output=json&ak=jIHHpazvMV0a8o5qy5Kw8q1nvnMIhVCQ`;   // 百度地址联想api
                that.$http.jsonp(url).then((res)=> {
                    this.listShowHide = true;
                    that.processData(res.body)
                },(err)=> {
                    console.log(err)
                })
            }
        },
        onInput () {
            this.onFocus();
        },
        // 最大的坑 click和blur事件冲突,解决方法使用定时器延迟触发blur时间,时间控制在150以内
        onListClick (value) {
            this.addressValue = value;
            this.listShowHide = false;
        },
        onBlur () {
            setTimeout(()=>{
                this.listShowHide = false;
            },150)
        },
        processData (data) {
            let addressList = [];
            let addressLists = data.result;
            for (let i = 0; i < addressLists.length; i++) {
                if (addressLists[i].city && addressLists[i].district != "") {
                    addressList.push({ text: addressLists[i].name, area: '(' + addressLists[i].city + addressLists[i].district + ')' })
                } else {
                    addressList.push({ text: addressLists[i].name, area: '' })
                }
            }
            this.addressList = addressList;
        }
    }
}
</script>

<style>
*{
    margin: 0;
    padding: 0;
    list-style: none;
}
#app{
    padding-top: 30px;
}
.container{
    position: relative;
    width: 250px;
    margin: 0 auto;
}
.container label{
    width: 50px;
    display: inline-block;
    text-align: right;
}
input{
    outline: none;
    width: 185px;
    height: 30px;
    border:1px solid #ccc;
    border-radius: 5px;
    padding-left: 5px;
}
ul{
    border:1px solid #ccc;
    width: 190px;
    position: absolute;
    right: 4px;
    border-radius: 5px;
}
ul li{
    min-height: 30px;
    line-height: 30px;
    padding-left: 1px;
    border-bottom: 1px solid #eaeaea;
    color: #666;
    font-size: 12px;
    white-space: wrap;
}
ul li:last-child{
    border: none;
}
</style>
