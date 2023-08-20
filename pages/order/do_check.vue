<template>
  <view class="container">
	  <view style="text-align: center;">
		<view style="margin: 20rpx;">
			<u-steps :list="numList" :current="progress"></u-steps>
		</view>
		<view style="margin: 20rpx;">
			<u-cell-group :border="false">
				<u-cell-item icon="arrow-right-double" title="待盘点设备" :arrow="false">{{waitnum}}台</u-cell-item>	
			</u-cell-group>
		</view>
		<view style="margin: 30rpx;">
			<view @tap="scan()">
			  <u-icon name="scan" color="#2979ff" size="180"></u-icon>
			</view>	
		</view>
		<view class="grid" style="padding-left: 30rpx;padding-right: 30rpx;" v-if="item.typename ==undefined" v-show="false">
			<u-form :model="form" ref="uForm">
				<u-form-item label="设备类别:" label-width="130"><u-input :value="item.typename" disabled="true" input-align="right"/></u-form-item>				
				<u-form-item label="序列号:" label-width="130"><u-input :value="item.sn" disabled="true" input-align="right"/></u-form-item>
				<u-form-item label="使用单位:" label-width="130"><u-input :value="item.organ" disabled="true" input-align="right"/></u-form-item>
				<u-form-item label="盘点年度:" label-width="130"><u-input :value="item.checkyear" disabled="true" input-align="right"/></u-form-item>
				<u-form-item label="状态:" label-width="130"><u-input :value="item.device_status" disabled="true" input-align="right"/></u-form-item>				
			</u-form>
		</view>
		<view class="grid" style="padding-left: 30rpx;padding-right: 30rpx;" v-else v-show="true">
			<u-form :model="form" ref="uForm">
				<u-form-item label="设备类别:" label-width="130"><u-input :value="item.typename" disabled="true" input-align="right"/></u-form-item>				
				<u-form-item label="序列号:" label-width="130"><u-input :value="item.sn" disabled="true" input-align="right"/></u-form-item>
				<u-form-item label="使用单位:" label-width="130"><u-input :value="item.organ" disabled="true" input-align="right"/></u-form-item>
				<u-form-item label="盘点年度:" label-width="130"><u-input :value="item.checkyear" disabled="true" input-align="right"/></u-form-item>
				<u-form-item label="设备状态:" label-width="130">
					<view style="float:right">
					<u-radio-group v-model="value" @change="radioGroupChange">
						<u-radio 
							@change="radioChange" 
							v-for="(item, index) in list" :key="index" 
							:name="item.name"
							:disabled="item.disabled"
						>
							{{item.name}}
						</u-radio>
					</u-radio-group>
					</view>
				</u-form-item>
				<view style="margin-top: 20rpx;">
					<u-button type="primary" shape="square" @click="updateById()">盘点提交</u-button>
				</view>
			</u-form>
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
		  item:{},
		  progress:0,
	      numList: [{
				name: '扫码识别'
			}, {
				name: '确认信息'
			}, {
				name: '标记状态'
			}, {
				name: '完成盘点'
			}, ],
		   result:'',
		   waitnum:'',
		   organId:'',
		   organname:'',
		   device_status:'',
		   list: [
				{
					name: '正常',
					disabled: false
				},
				{
					name: '故障',
					disabled: false
				},
				{
					name: '报废',
					disabled: false
				}
			],
		   			// u-radio-group的v-model绑定的值如果设置为某个radio的name，就会被默认选中
		   value: '',		   
	    };
	  },
	  watch: {
		
	  },
	  onLoad(options) {
	    that = this;
	    vk = that.vk;
	    that.init(options);	
		that.getUserOrgan();
		console.log(that.item.typename)
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
		radioChange(e) {			
			console.log(e)
			that.device_status = e;
			that.progress = 2;
		},
		// 选中任一radio时，由radio-group触发
		radioGroupChange(e) {
			console.log(e);
		},
		getUserOrgan(){
			vk.userCenter.getCurrentUserInfo({
			  success:function(data){
			    // 成功后的逻辑
			    that.organId = data.userInfo.organ_id;
				that.getOrgan();
			  }
			});
		},
		getOrgan(){
			let data = {
			  organcode: this.organId
			};
			vk.callFunction({
			  url: 'client/organ/kh/findById',
			  title: '请求中...',
			  data: data,
			  success(data) {	
				 console.log(data)
				 that.organname = data.item.organname;
				 that.getCheckTask();
			  }
			});
		},
		getCheckTask(){
			let data = {
			  organname: this.organname
			};
			vk.callFunction({
			  url: 'client/order/kh/getTask',
			  title: '请求中...',
			  data: data,
			  success(data) {	
				 console.log(data)				 
				 that.waitnum = data.num;				 
			  }
			});
		},
		scan(){
		 uni.scanCode({
		     success: function (res) {
		         console.log('条码类型：' + res.scanType);
		         console.log('条码内容：' + res.result);
				 that.result = res.result;
				 that.getInfo();
				 that.progress = 1;
				 uni.showToast({
				     title: '识别成功',
				     duration: 1000,
					  success() {
						 setTimeout(function() {
							
						 }, 1000);					  	 
					  }
				 });			     
		     }
		 });
		},
		getInfo() {
		  let data = {
		    _id: this.result
		  };
		  vk.callFunction({
		    url: 'client/order/kh/findById',
		    title: '请求中...',
		    data: data,
		    success(data) {
		       if(!data.item){
				   uni.showToast({
				       title: '待盘点设备不存在',
				       duration: 2000,
					   icon:'error',
				   	  success() {
				   		 setTimeout(function() {						 
				   		 vk.reLaunch({
				   		   url: '../home/home'  //跳转传参写法
				   		 });
				   		 }, 2000);					  	 
				   	  }
				   });
			   }else if(data.item.status == 0){
					uni.showToast({
					    title: '设备已盘点',
					    duration: 2000,
						icon:'error'						  
					});
			   }else{
				   that.item = data.item;	
			   }		       	      
		    }
		  });
		},
		updateById() {				
				vk.callFunction({
				  url: 'client/order/kh/updateById',
				  data: {
					  device_status:that.device_status,
					  _id:that.item._id					  
				  },
				  title: '请求中...',
				  success(data) {
					that.progress = 3;
					uni.showToast({
						title: '盘点成功',
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