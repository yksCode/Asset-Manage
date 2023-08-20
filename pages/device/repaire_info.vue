<template>
  <view class="container">
    <view class="grid" style="padding-left: 30rpx;padding-right: 30rpx;">
		<u-form :model="form" ref="uForm">
			<u-form-item label="设备类别:" label-width="130"><u-input :value="item.typename" disabled="true" input-align="right"/></u-form-item>	
			<u-form-item label="品牌:" label-width="130"><u-input :value="item.brand" disabled="true" input-align="right"/></u-form-item>
			<u-form-item label="型号:" label-width="130"><u-input :value="item.model" disabled="true" input-align="right"/></u-form-item>
			<u-form-item label="序列号:" label-width="130"><u-input :value="item.sn" disabled="true" input-align="right"/></u-form-item>
			<u-form-item label="使用单位:" label-width="130"><u-input :value="item.organ" disabled="true" input-align="right"/></u-form-item>
			<u-form-item label="报修原因:" label-width="130"><u-input :value="item.reason" disabled="true" input-align="right"/></u-form-item>
			<u-form-item label="故障图片:" label-width="130">
				<view v-for="(item,index) in item.fileID" :key="item">
					<u-image width="100%" height="300rpx" :src="item" style="margin: 10rpx;" @click="onPreview(item)"></u-image>
				</view>
			</u-form-item>
			<u-form-item label="报修人:" label-width="130"><u-input :value="item.reportman" disabled="true" input-align="right"/></u-form-item>			
			<u-form-item label="报修时间:" label-width="130"><u-input :value="item._add_time_str" disabled="true" input-align="right"/></u-form-item>
			<view v-if="item.maintenanceTime==undefined" v-show="false">
			<u-form-item label="处理时间:" label-width="130"><u-input :value="item.maintenanceTime" disabled="true" input-align="right"/></u-form-item>
			</view>
			<view v-else v-show="true">
			<u-form-item label="处理时间:" label-width="130"><u-input :value="item.maintenanceTime" disabled="true" input-align="right"/></u-form-item>
			</view>
			<u-form-item label="状态:" label-width="130"><u-input :value="item.status" disabled="true" input-align="right"/></u-form-item>
			<!--<u-form-item label="操作:" label-width="130"><u-button type="primary" size="mini" shape="square" @click="add()" style="float: right;">故障处理</u-button></u-form-item>-->			
		</u-form>
    </view>    
    <i-message id="message"></i-message>
  </view>

</template>

<script>
	var that; // 当前页面对象
	var vk; // vk依赖	
	export default {
	  data() {
	    return {
		   show: false,	   		  
		   _id: '',
		  tempFilePath:'',
	      data: {},
	      item: {},		  
	    };
	  },	  
	  onLoad(options) {		
	    this._id = options._id;		
		console.log(this._id);
		that = this;
	    vk = that.vk;
	    that.init(options);
		that.findById();//解决第一次点击下拉为空，第二次才有值的问题；
	  },
	  // 必须要在onReady生命周期，因为onLoad生命周期组件可能尚未创建完毕
	  onReady() {
		  
	  },
	  methods: {		
	    // 页面数据初始化函数
	    init(options) {
	      
	    },
	    pageTo(url) {
	      vk.navigateTo(url);
	    },
		onPreview(item) {
			let photoList = [];
			    photoList.push(item);
			uni.previewImage({				
				current:'',
				urls: photoList	 //必须是数组			
			});
		},
		findById() {
		  let data = {
		    _id: this._id
		  };
		  vk.callFunction({
		    url: 'client/device/sys/findRepaireById',
		    title: '请求中...',
		    data: data,
		    success(data) {
		       if(!data.item){
				   uni.showToast({
				       title: '设备信息不存在',
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
			   }else{
				   that.item = data.item;	
			   }		       	      
		    }
		  });
		},
	    update() {
	      let data = {
	        _id: that.getFirstId()
	      };
	      vk.callFunction({
	        url: 'template/db_api/pub/update',
	        title: '请求中...',
	        data: data,
	        success(data) {
	          vk.alert(JSON.stringify(data));
	          that.item = data;	          
	        }
	      });
	    },
	    updateById() {
	      let data = {
	        _id: that.getFirstId()
	      };
	      vk.callFunction({
	        url: 'template/db_api/pub/updateById',
	        title: '请求中...',
	        data: data,
	        success(data) {
	          vk.alert(JSON.stringify(data));
	          that.item = data;
	          that.getList();
	        }
	      });
	    },
	    updateAndReturn() {
	      let data = {
	        _id: that.getFirstId()
	      };
	      vk.callFunction({
	        url: 'template/db_api/pub/updateAndReturn',
	        title: '请求中...',
	        data: data,
	        success(data) {
	          vk.alert(`当前money的值：${data.info.money} \n 提示：此为原子操作，可以应用于类似id自增，数字自减等业务。`);
	          that.item = data;
	          that.getList();
	        }
	      });
	    }
	  }
	}
</script>
<style lang="scss" src="@/static/styles/common_form.scss">

</style>