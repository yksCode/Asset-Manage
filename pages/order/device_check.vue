<template>
  <view class="container">
	  <view style="text-align: center;">
		<view style="margin: 20rpx;">
			<u-button type="primary" shape="square" size="medium" @click="getAll()">统计待盘</u-button>
		</view>
		<view style="margin: 20rpx;">
			<u-cell-group :border="false">
				<u-cell-item icon="arrow-right-double" title="待盘点设备" :arrow="false">{{waitnum}}台</u-cell-item>	
			</u-cell-group>
		</view>
		<view style="margin: 20rpx;">
			<u-button type="primary" shape="square" size="medium" @click="adds()">生成任务</u-button>	
		</view>
	  </view>
  </view>

</template>

<script>
	var that; // 当前页面对象
	var vk; // vk依赖	
	export default {
	  data() {
	    return {		   
	      data:{}, //请求数据必须是对象，不能是数组
	      item: {},
		  waitnum:''
	    };
	  },
	  watch: {
		
	  },
	  onLoad(options) {
	    that = this;
	    vk = that.vk;
	    that.init(options);		
	  },
	  // 必须要在onReady生命周期，因为onLoad生命周期组件可能尚未创建完毕
	  onReady() {		 	 
	  },
	  methods: {
	    // 页面数据初始化函数
	    init(options) {
	      
	    },
	    pageTo(url) {
	      vk.reLaunch(url);
	    },
		getAll(){			
			vk.callFunction({
			  url: 'client/order/sys/getList',
			  data: '',
			  title: '请求中...',
			  success(data) {								
				that.data = data.rows;
				that.waitnum = data.total;
				console.log(that.data);
			    uni.showToast({
			        title: '统计成功',
			        duration: 2000				  
			    });	          
			  }
			});
		},
		adds() {
		  var datajson = that.data;
			  console.log(datajson);		  
		  vk.callFunction({
		    url: 'client/order/sys/adds',
			data: {datajson},
		    title: '请求中...',
		    success(data) {
		      uni.showToast({
		          title: '生成成功',
		          duration: 2000,
					  success() {
						 setTimeout(function() {						
						 that.pageTo("../home/home");
						 }, 2000);					  	 
					  }				  
		      });	          
		    }
		  });
		}
	  }
	}
</script>
<style lang="scss" src="@/static/styles/common_form.scss">

</style>