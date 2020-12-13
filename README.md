# -
homework

小程序





js






var delay =1;
var date=0;
var util = require(‘../../utils/util.js’);
Page({

data: {
temperature:”0″,
humidity: “0”,
kaiguan:”0″,
day: 0,
delay:0,

},
onLoad: function () {//打开小程序后自动获取当天日期
// 调用函数时，传入new Date()参数，返回值是日期和时间
var time = util.formatTime(new Date());
// 再通过setData更改Page()里面的data，动态更新页面的数据
this.setData({
day: time
});
date =this.data.day;
var that = this
wx.request({//打开小程序后自动获取是否洗完衣服和
url: “https://api.heclouds.com/devices/649143237/datapoints”, //将请求行中的数字换成自己的设备ID
header: {
“api-key”: “jlqxawPNJZ60YtM2k23GPjx18yA=” //换成自己的api-key
},
data: {
limit: 1
},
method: “GET”,
success: function (e) {
console.log(e.data.data)
that.setData({

day: e.data.data.datastreams[2].datapoints[0].value,
kaiguan: e.data.data.datastreams[1].datapoints[0].value,
})
}
});
//如果洗完则比较是否已逾期一天
if (that.data.day==0 && that.data.kaiguan==1){
let deviceid = “649143237”
let apikey = “jlqxawPNJZ60YtM2k23GPjx18yA=”
let data={
“datastreams”: [
{“id”: “time”,”datapoints”:[{“value”:date}]},

]
}
wx.request({
url: “https://api.heclouds.com/devices/649143237/datapoints”,
method:’POST’,
header:{
“content-type”: ‘application/json’,
“api-key”: “jlqxawPNJZ60YtM2k23GPjx18yA=”
},
data:JSON.stringify(data),
success(res){
console.log(“更新数据成功”,res)
},
fail: function(res){
wx.showToast({ title: ‘系统错误’ })
},
complete:function(res){
wx.hideLoading()
}
})

}
else if(that.data.day!=date && that.data.kaiguan==1){this.setData({delay :1})}
else{this.setData({delay :0})}
console.log(delay)
},
OneNet_Post:function(){//次函数用于重置oneNet上的数据
var that=this
let deviceid = “649143237”
let apikey = “jlqxawPNJZ60YtM2k23GPjx18yA=”
let data={
“datastreams”: [
{“id”: “jiao”,”datapoints”:[{“value”:0}]},

{“id”: “time”,”datapoints”:[{“value”:0}]},
]
}
wx.request({
url: “https://api.heclouds.com/devices/649143237/datapoints”,
method:’POST’,
header:{
“content-type”: ‘application/json’,
“api-key”: “jlqxawPNJZ60YtM2k23GPjx18yA=”
},
data:JSON.stringify(data),
success(res){
console.log(“更新数据成功”,res)
},
fail: function(res){
wx.showToast({ title: ‘系统错误’ })
},
complete:function(res){
wx.hideLoading()
}
})
this.setData({delay :0})
},
//周期函数
/*onLoad: function () {
setInterval(function () {
this.init() //不断获取继电器状态
}.bind(this), 500)
},*/

//用于获取继电器状态
init: function () {
var that = this
wx.request({//从oneNet获取洗衣状态
url: “https://api.heclouds.com/devices/649143237/datapoints”, //将请求行中的数字换成自己的设备ID
header: {
“api-key”: “jlqxawPNJZ60YtM2k23GPjx18yA=” //换成自己的api-key
},
data: {
limit: 1
},
method: “GET”,
success: function (e) {
console.log(e.data.data)
that.setData({

kaiguan: e.data.data.datastreams[1].datapoints[0].value,
day: e.data.data.datastreams[2].datapoints[0].value,
})
}
});

},
makenoise:function(){//此函数用于改变oneNet数值使硬件端工作
var that=this
let deviceid = “649143237”
let apikey = “jlqxawPNJZ60YtM2k23GPjx18yA=”
let data={
“datastreams”: [

{“id”: “jiao”,”datapoints”:[{“value”: 1}]},
]
}
wx.request({
url: “https://api.heclouds.com/devices/649143237/datapoints”,
method:’POST’,
header:{
“content-type”: ‘application/json’,
“api-key”: “jlqxawPNJZ60YtM2k23GPjx18yA=”
},
data:JSON.stringify(data),
success(res){
console.log(“更新数据成功”,res)
},
fail: function(res){
wx.showToast({ title: ‘系统错误’ })
},
complete:function(res){
wx.hideLoading()
}
})
}

})

 




wxss：







.OneNet {
height: 100vh;
flex-direction: column;
}

.list {
height: 95vh;
}

.course {
position: relative;
margin-top: 47px;
text-align: center;
}

.ImageM {
width: 20%;
}

.varFont {
font-size: 20px;
color: gray;
}

.varFontRed {
font-size: 20px;
color: red;
}

.text {
position: relative;
text-align: center;
}

.textFont {
font-size: 20px;
color: gray;
}

.Var {
position: relative;
text-align: center;
}
.bt_1{
color: black;
margin-top: 40rpx;
background: linear-gradient(#ccfbff, #ef96c5);
}
.bt_2{
margin-top: 40rpx;
background: linear-gradient(to right, #ead6ee, #a0f1ea);
}
.bt_3{
margin-top: 40rpx;
background: linear-gradient(to bottom right, #eebd89, #d13abd);
}









wxml：









<view calss=”OneNet”>

<view class=”course”>
<view>
<image class=”ImageM” mode=”widthFix” src=”../image/humi.png”></image>
</view>
<view class=”text”>
<text class=”textFont”>洗衣状况</text>
</view>
<view class=”Var”>
<text class=”varFont” > </text> <text> </text>
<view class=”textFont” wx:if=”{{kaiguan==1 }}”>已洗完</view>
<view class=”textFont” wx:if=”{{kaiguan==0 }}”>未洗完</view>
<view class=”textFont” wx:if=”{{delay==1 }}”>请尽快取衣</view>
<view wx:if=”{{delay==0 }}”> </view>
</view>
</view>
</view>
<button class=”bt_2″ bindtap=’init’ >查看</button>
<button class=”bt_3″ bindtap=’OneNet_Post’ >重置</button>
<button class=”bt_1″ bindtap=’makenoise’>响铃</button>
