<template>
  <view class="container" style="padding-left: 30rpx;padding-right: 30rpx;"> 
    <u-form :model="form" ref="uForm">
		<u-form-item label="名称:" label-width="130"><u-input v-model="form1.organname" input-align="right"/></u-form-item>
		<u-form-item label="机构码:" label-width="130"><u-input v-model="form1.organcode" input-align="right"/></u-form-item>			
		<view class="btn-box">
		  <view class="biz-btn save" @tap="add">提交</view>
		</view>
	</u-form>        
  </view>
</template>

<script>
	var that; // 当前页面对象
	var vk; // vk依赖	
	export default {
	  data() {
	    return {
	      form1:{
	      	status: 0,
	      	organname: '',
			organcode:''
	      },
	      data: {},
	      item: {},		  
	    };
	  },
	  onLoad(options) {
	    that = this;
	    vk = that.vk;
	    that.init(options);
	  },
	  methods: {
	    // 页面数据初始化函数
	    init(options) {
	      
	    },
	    pageTo(url) {
	      vk.reLaunch(url);
	    },		
	    add() {
		  const {status, organname,organcode} = that.form1;
		  var form1 = that.form1;
	      vk.callFunction({
	        url: 'client/organ/sys/add',
			data: form1,
	        title: '请求中...',
	        success(data) {
	          uni.showToast({
	              title: '添加成功',
	              duration: 2000,
				  success() {
					 setTimeout(function() {
					 that.pageTo("../index/index")
					 }, 2000);					  	 
				  }
	          });
	          that.item = data;	          
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
	          that.getList();
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