<template>
  <view class="container">    
    <i-message id="message"></i-message>
    <view class="header">
      <!--封面图片区-->
      <view class="header">
        <u-swiper :list="list"></u-swiper>
      </view>
      <!--管理员功能列表区-->
      <!-- <view class="pannel-header" :style="'display:' + (userRole > 2?'none':'flex')">
        <view class="pannel-title">
          <view class="title">管理员控制台 </view>
          <view class="sub-title"> ( 仅管理员可见 ) </view>
        </view>
      </view> -->
	  <view class="pannel-header">
	    <view class="pannel-title">
	      <view class="title">管理员控制台 </view>
	      <view class="sub-title"> ( 仅管理员可操作 ) </view>
	    </view>
	  </view>
      <view class="pannel-content">
        <view @tap="pageTo('../device/device_list')">
          <view>
             <u-icon name="plus-square-fill" color="#2979ff" size="80"></u-icon>
          </view>
          <view class="fun-txt">设备管理</view>
        </view>
        <view @tap="pageTo('../type/type_list')">
          <view>
            <u-icon name="grid" color="#2979ff" size="80"></u-icon>
          </view>
          <view class="fun-txt">类别管理</view>
        </view>
        <view @tap="pageTo('../organ/organ_list')">
          <view>
            <u-icon name="setting" color="#2979ff" size="80"></u-icon>
          </view>
          <view class="fun-txt">更新照片</view>
        </view>
        <view @tap="pageTo('../person/person_list')">
          <view>
             <u-icon name="man-add" color="#2979ff" size="80"></u-icon>
          </view>
          <view class="fun-txt">用户管理</view>
        </view>		
      </view>
	  <!--管理员列表区2-->
	  <view class="pannel-content">
	    <view @tap="pageTo('../device/repaire_list')">
	      <view>
	         <u-icon name="lock-open" color="#2979ff" size="80"></u-icon>
	      </view>
	      <view class="fun-txt">报修处理</view>
	    </view>
	    <view @tap="pageTo('../device/apply_list')">
	      <view>
	        <u-icon name="coupon" color="#2979ff" size="80"></u-icon>
	      </view>
	      <view class="fun-txt">采购处理</view>
	    </view>
	    <view @tap="pageTo('../order/device_check')">
	      <view>
	         <u-icon name="clock" color="#2979ff" size="80"></u-icon>
	      </view>
	      <view class="fun-txt">盘点任务</view>
	    </view>
		<view @tap="pageTo('../charts/charts')">
	      <view>
	        <u-icon name="photo" color="#2979ff" size="80"></u-icon>
	      </view>
	      <view class="fun-txt">报表统计</view>
	    </view>	    		
	  </view>
      <!--普通导航功能列表区-->
      <view class="pannel-header">
        <view class="pannel-title">
          <view class="title">设备管理</view>
          <view class="sub-title"></view>
        </view>        
      </view>
      <view class="pannel-content">
        <view @tap="openScan()">
          <view>
            <u-icon name="scan" color="#2979ff" size="80"></u-icon>
          </view>
          <view class="fun-txt">设备识别</view>
        </view>
        <view @tap="repaireScan()">
          <view>
            <u-icon name="warning" color="#2979ff" size="80"></u-icon>
          </view>
          <view class="fun-txt">设备报修</view>
        </view>
        <view @tap="pageTo('../order/do_check')">
          <view>
            <u-icon name="camera" color="#2979ff" size="80"></u-icon>
          </view>
          <view class="fun-txt">设备盘点</view>
        </view>
        <view @tap="pageTo('../device/device_apply')">
          <view>
            <u-icon name="shopping-cart" color="#2979ff" size="80"></u-icon>
          </view>
          <view class="fun-txt">设备申请</view>
        </view>
      </view>      
    </view>    
  </view>
</template>

<script>
var that;		// 当前页面对象
var vk;			// vk依赖
export default {
  data() {
    return {

        companyName: '',
        //激活的公司
        companyId: 0,
        //激活的公司ID
        list: [
			{
				image: '../../static/images/head.jpg',
				title: '511固定资产运维管理'
			},
			{
				image: '../../static/images/head_1.jpg',
				title: '511固定资产运维管理'
			}
		],
        tmplIds: {},
        //需用户订阅的模版消息ID
        syncMsgList: {},
    };
  },

  // 监听 - 页面每次【加载时】执行(如：前进)
  onLoad(options = {}) {
  	that = this;
  	vk = that.vk;
  	that.options = options;
  	that.init(options);
  },

  onShow() {
    
  },

  onPullDownRefresh() {
  
  },  
  methods: {
	 // 页面数据初始化函数
	 init(options) {
	 	console.log("init: ", options);	 	
	 },
	 pageTo(path) {
	 	vk.navigateTo(path);
	 },
	 openScan(){
		 uni.scanCode({
		     success: function (res) {
		         console.log('条码类型：' + res.scanType);
		         console.log('条码内容：' + res.result);
				 uni.showToast({
				     title: '识别成功',
				     duration: 1000,
					  success() {
						 setTimeout(function() {
						vk.navigateTo({
							url: '../device/device_scan?_id='+res.result  //跳转传参写法
						});
						 }, 1000);					  	 
					  }
				 });			     
		     }
		 });
	 },
	 repaireScan(){
		 uni.scanCode({
		     success: function (res) {
		         console.log('条码类型：' + res.scanType);
		         console.log('条码内容：' + res.result);
		 				 uni.showToast({
		 				     title: '识别成功',
		 				     duration: 1000,
		 					  success() {
		 						 setTimeout(function() {
		 						vk.navigateTo({
		 							url: '../device/device_repaire?_id='+res.result  //跳转传参写法
		 						});
		 						 }, 1000);					  	 
		 					  }
		 				 });			     
		     }
		 });
	 }
  },
  computed: {    

  },
  watch: {}
};
</script>
<style >
  .form_button {
    background-color: transparent;
    padding: 0;
    margin: 0;
    text-align: left;
  }
  .form_button::after {
    border: 0px;
  }

</style>
<style lang="scss" src="@/static/styles/index.scss">

</style>