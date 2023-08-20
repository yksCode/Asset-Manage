<template>
  <view class="container">
    <view class="grid" style="padding-left: 30rpx;padding-right: 30rpx;">
		<u-form :model="form" ref="uForm">					
			<u-form-item label="用户ID:" prop="_id" label-width="130"><u-input v-model="form._id" placeholder="" input-align="right" disabled="true" /></u-form-item>
			<u-form-item label="用户名:" prop="username" label-width="130"><u-input v-model="form.username" placeholder="" input-align="right" disabled="true"/></u-form-item>
			<u-form-item label="昵称:" prop="nickname" label-width="130"><u-input v-model="form.nickname" placeholder="请填写用户昵称" input-align="right"/></u-form-item>					
			<u-form-item label="后台权限:" label-width="130">				
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
			<u-form-item label="所在单位:" label-width="80">
				<view style="text-align: right;" @click="selectOrganShow = true">
					<label style="padding-left: 11px;" @tap="getOrgan()">
						<view>{{ organResult }}</view>
					</label>
					<u-select :default-value="defaultOrganValue" mode="single-column" v-model="selectOrganShow" :list="listOrgan" @confirm="confirmOrgan"	@cancel="cancelOrgan">
					</u-select>
				</view>
			</u-form-item>
			<view style="margin-top: 20rpx;">
				<u-button type="primary" shape="square" @click="updateById()">提交</u-button>
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
	      form:{      	
			username: '',
			nickname:'',			
			role:[],
			allow_login_background:false			
	      },
		  list: [
				{
					name: '超级管理员',
					disabled: false
				}
			],
			// u-radio-group的v-model绑定的值如果设置为某个radio的name，就会被默认选中
		  value: '',
		  _id:'',
	      data: {},		 
	      item: {},	
		  organResult: '请选择',
		  selectOrganShow:false,
		  keyaOrgan: '',
		  defaultOrganValue: [1], //默认选项
		  listOrgan: [{
		  				lbael: 0,
		  				value: null
		  }],
	    };
	  },
	  watch: {
		keyaOrgan(val) {
			this.$emit('labelnumorgan', this.keyaOrgan);
		},
	  },
	  onLoad(options) {
	    this._id = options._id;	    
	    console.log(this._id);
		that = this;
	    vk = that.vk;
	    that.init(options);
		that.findById();//解决第一次点击下拉为空，第二次才有值的问题；
		that.getOrgan();//解决第一次点击下拉为空，第二次才有值的问题；
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
			if(e=='超级管理员'){
				that.form.allow_login_background = true;
				that.form.role = 'admin';
			}
		},
		// 选中任一radio时，由radio-group触发
		radioGroupChange(e) {
			console.log(e);
		},
		// 注意返回值为一个数组，单列时取数组的第一个元素即可(只有一个元素) 
		findById() {
		  let data = {
		    _id: this._id
		  };
		  vk.callFunction({
		    url: 'client/person/sys/findById',
		    title: '请求中...',
		    data: data,
		    success(data) {
		       if(!data.item){
				   uni.showToast({
				       title: '用户信息不存在',
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
				   that.form = data.item;				   
			   }		       	      
		    }
		  });
		},  		
	    updateById() {
				const {role,allow_login_background} = that.form;
				var up_data = that.form;					
					up_data._id = that._id;
					up_data.organ_id = that.keyaOrgan;
				vk.callFunction({
				  url: 'client/person/sys/updateById',
				  data: up_data,
				  title: '请求中...',
				  success(data) {
					uni.showToast({
						title: '更新成功',
						duration: 2000,
						  success() {
							 setTimeout(function() {
							 that.item = data;
							 that.pageTo("../home/home");
							 }, 2000);					  	 
						  }
					});	          
				  }
				});
	    },
		confirmOrgan(e) {
			that.listOrgan = [];
			that.organResult = '';
			that.keyaOrgan = '';
			that.getOrgan();  //解决点击确认后，下拉值空的问题；
			e.map((val, index) => {
				let organResult = val.label
				that.organResult += val.label;
				let keyaOrgan = val.value;
				that.keyaOrgan += val.value
			})
			console.log(that.keyaOrgan);
		},
		cancelOrgan(e) {
			
		},
		getOrgan() {
			vk.callFunction({
			  url: 'client/organ/sys/select',
			  title: '请求中...',			  
			  success:function(data){				
			    //let server = res.data.data;
			    let values = data.rows;				
			    for (var i = 0; i < values.length; i++) {
			    	var obj = {
			    		label: values[i].organname,
			    		value: values[i].organcode,
			    	};	
					//console.log(obj);
			    	that.listOrgan.push(obj);
			    }
			  },
			  fail:function(){
			  	vk.alert('请求失败，请再次操作！');
			  }
			});			
		},
	  }
	}
</script>
<style lang="scss" src="@/static/styles/common_form.scss">

</style>