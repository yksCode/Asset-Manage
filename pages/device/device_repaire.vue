<template>
  <view class="container">
    <view class="grid" style="padding-left: 30rpx;padding-right: 30rpx;">
		<u-form :model="form" ref="uForm">
			<u-form-item label="设备类别:" prop="typename" label-width="130"><u-input v-model="form.typename" placeholder="请填写报修设备类别" input-align="right"/></u-form-item>			
			<u-form-item label="品牌:" prop="brand" label-width="130"><u-input v-model="form.brand" placeholder="请填写报修设备品牌" input-align="right"/></u-form-item>
			<u-form-item label="型号:" prop="model" label-width="130"><u-input v-model="form.model" placeholder="请填写报修设备型号" input-align="right"/></u-form-item>
			<u-form-item label="序列号:" prop="sn" label-width="130"><u-input v-model="form.sn" placeholder="请填写报修设备序列号" input-align="right"/></u-form-item>
			<u-form-item label="使用单位:" prop="organ" label-width="130"><u-input v-model="form.organ" placeholder="请填写报修单位" input-align="right"/></u-form-item>	
			<u-form-item label="报修人:" prop="reportman" label-width="130"><u-input v-model="form.reportman" placeholder="请填写报修人姓名" input-align="right"/></u-form-item>			
			<u-form-item label="故障描述:" label-width="130" prop="reason"><u-input v-model="form.reason" placeholder="描述故障点" input-align="right"/></u-form-item>
			<u-form-item label="现场图片:" label-width="130">				
				<vk-data-upload
				uploadText="故障现象图片"
				v-model="fileID"
				:limit="3"
				:showHeader="false"
				provider="unicloud"
				></vk-data-upload>
			</u-form-item>
			<u-form-item label="接收维修进度通知:" label-width="160" v-show="buttonshow">
				<view style="float: right;">
					<u-button type="success" @click="requestSubscribe()">接收消息通知</u-button>
				</view>
			</u-form-item>
			<view style="margin-top: 20rpx;" v-show="submitshow">
				<u-button type="primary" shape="square" @click="add()">提交报修</u-button>
			</view>
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
		  data: {
		  			  thing1: {
		  			    DATA: ""
		  			  },
		  			  thing2: {
		  			    DATA: ""
		  			  },
		  			  phrase16: {
		  			    DATA: ""
		  			  },
		  			  status: 1, //发送状态 0表示已发送，1表示未发送
		  			  templateId: 'prRG9gFVZgy0pm0NAowi1P-X-nCp9bANlLilO03LxVY' ,// 模板ID
					  openid:'',
					  dev_id:''
		  },
		  buttonshow:false,
		  submitshow:true,
	      form:{
	      	status: '未处理',
	      	dev_id:'',
			typename: '',
			brand:"",
			model:"",
			sn:"",
			organ:"",
			reportman:"",
			user_id:'',
			reason:""			
	      },
		  fileID:[], //数组
		  rules: {
				typename: [
					{ 
						required: true, 
						message: '请选择类别', 
						// 可以单个或者同时写两个触发验证方式 
						trigger: ['change','blur'],
					}
				],
				brand: [
					{ 
						required: true, 
						message: '请填写设备品牌', 
						// 可以单个或者同时写两个触发验证方式 
						trigger: ['change','blur'],
					}
				],
				model: [
					{ 
						required: true, 
						message: '请填写设备型号', 
						// 可以单个或者同时写两个触发验证方式 
						trigger: ['change','blur'],
					}
				],
				sn: [
					{ 
						required: true, 
						message: '请填写设备序列号', 
						// 可以单个或者同时写两个触发验证方式 
						trigger: ['change','blur'],
					}
				],
				reason: [
					{ 
						required: true, 
						message: '请填写设备故障详情', 
						// 可以单个或者同时写两个触发验证方式 
						trigger: ['change','blur'],
					}
				]					
		   }	     	  
	    };
	  },
	  watch: {
		
	  },	  	  
	  onLoad(options) {
	    this._id = options._id;	   
	    console.log(this._id);
	    that = this;
	    vk = that.vk;
	    that.init(options);
		that.sendMessage();
	    that.findById();//解决第一次点击下拉为空，第二次才有值的问题；
	  },
	  // 必须要在onReady生命周期，因为onLoad生命周期组件可能尚未创建完毕
	  onReady() {
		  this.$refs.uForm.setRules(this.rules);		 
	  },
	  methods: {
	    // 页面数据初始化函数
	    init(options) {
	      
	    },
	    pageTo(url) {
	      vk.reLaunch(url);
	    },		
		findById() {
		  let data = {
			_id: this._id
		  };
		  vk.callFunction({
			url: 'client/device/kh/findById',
			title: '请求中...',
			data: data,
			success(data) {
			   if(!data.item){
				   uni.showToast({
					   title: '设备信息不存在',
					   duration: 1000,
					   icon:'error',
					  success() {
						 setTimeout(function() {						 
						 vk.reLaunch({
						   url: '../home/home'  //跳转传参写法
						 });
						 }, 1000);					  	 
					  }
				   });
			   }else if(data.item.status=="故障"){
				   uni.showToast({
					   title: '设备已报修',
					   duration: 1000,
					   icon:'loading',
					  success() {
						 setTimeout(function() {						 
						 vk.reLaunch({
						   url: '../home/home'  //跳转传参写法
						 });
						 }, 1000);					  	 
					  }
				   });
				   
			   }else{
				   that.form = data.item;
				   that.form.reportman = data.userInfo.nickname;
				   that.form.user_id = data.userInfo._id;
			   }		       	      
			}
		  });
		},
		add(){			
			const {status,brand,model,sn,typename,organ,reportman,user_id,reason} = that.form;
			var add_data = that.form;
				add_data.status = '未处理';
				add_data.fileID = that.fileID;
				add_data.dev_id = this._id;
			console.log(add_data);
			vk.callFunction({
			  url: 'client/device/kh/add',
			  data: add_data,
			  title: '请求中...',
			  success(data) {
				that.submitshow = false;
				that.buttonshow = true;
				that.data.thing1.DATA = that.form.typename;
				that.data.thing2.DATA = that.form.reason;
				that.data.phrase16.DATA = that.form.status;
				that.data.dev_id = that._id;
			    uni.showToast({
			        title: '报修成功，请稍后',
			        duration: 2000,
					success() {
						 setTimeout(function() {
						 that.updateById();
						 //that.pageTo("../home/home");
						 }, 2000);					  	 
				    }
			    });	          
			  }
			});
		},
		msgadd() {
		  var msgdata = that.data;
		  console.log(msgdata);
		  vk.callFunction({
		    url: 'client/device/kh/addSubscribe',
			data: msgdata,
		    title: '请求中...',
		    success(data) {
		      uni.showToast({
		          title: '已成功订阅成功',
		          duration: 2000,
					  success() {
						 setTimeout(function() {							 
							that.pageTo("../home/home");
						 }, 2000);					  	 
					  }				  
		      });	          
		    }
		  });
		},
		 // 订阅接种通知
		requestSubscribe() {		   
		    // 发起订阅通知请求
			const templateId = this.data.templateId // 模板ID
		    uni.requestSubscribeMessage({
		      tmplIds: [templateId],
		      success: (res) => {
		        if (res[templateId] === 'accept') {
		          // 订阅成功，订阅记录存入数据库				  
				  	that.msgadd();
				    that.sendMessage();
					wx.showToast({
						icon: "success",
						title: '消息订阅成功',
					})
			  }else {
		          wx.showToast({
		            icon: 'none',
		            title: '消息订阅失败',
		          })
		          console.log(res[templateId], '失败')
		        }
		      }
		    })
		  },
		  sendMessage(obj) {
		  	// 需要先绑定微信
		  	new Promise((resolve, reject) => {
		  		// #ifdef MP-WEIXIN
		  		vk.userCenter.code2SessionWeixin({
		  			success:function(data){
		  				resolve(data.openid);
		  			}
		  		});
		  		// #endif
		  		// #ifndef MP-WEIXIN
		  		resolve(vk.getVuex('$user.userInfo.wx_openid.mp-weixin'));
		  		// #endif
		  	}).then((openid) => {
				that.data.openid = openid;		  		
		  	});	
		},
		updateById() {
		     	const {status} = that.form;
				var up_data = that.form;					
					up_data._id = that._id;
				vk.callFunction({
				  url: 'client/device/kh/updateById',
				  data: up_data,
				  title: '请求中...',
				  success(data) {
					  
				  }
				});
		}
	  }
	}
</script>
<style lang="scss" src="@/static/styles/common_form.scss">
	.vk-data-upload {
		width: 100%;
		.header-view{
			display: flex;
		}
		.left-view{
			width: 70%;
		}
		.right-view{
			width: 30%;
			text-align: right;
		}
	}
</style>