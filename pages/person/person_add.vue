<template>
  <view class="container">
    <view class="grid" style="padding-left: 30rpx;padding-right: 30rpx;">
		<u-form :model="form" ref="uForm">
			<u-form-item label="设备类别:" label-width="80">
				<view style="text-align: right;" @click="selectShow = true">
					<label style="padding-left: 11px;" @tap="getLightType()">
						<view>{{ result }}</view>
					</label>
					<u-select :default-value="defaultValue" mode="single-column" v-model="selectShow" :list="list" @confirm="confirm"	@cancel="cancel">
					</u-select>
				</view>				
			</u-form-item>			
			<u-form-item label="品牌:" prop="brand" label-width="130"><u-input v-model="form.brand" placeholder="请填写设备品牌" input-align="right"/></u-form-item>
			<u-form-item label="型号:" prop="model" label-width="130"><u-input v-model="form.model" placeholder="请填写设备型号" input-align="right"/></u-form-item>
			<u-form-item label="序列号:" prop="sn" label-width="130"><u-input v-model="form.sn" placeholder="请填写设备序列号" input-align="right"/></u-form-item>
			<u-form-item label="使用单位:" label-width="80">
				<view style="text-align: right;" @click="selectOrganShow = true">
					<label style="padding-left: 11px;" @tap="getOrgan()">
						<view>{{ organResult }}</view>
					</label>
					<u-select :default-value="defaultOrganValue" mode="single-column" v-model="selectOrganShow" :list="listOrgan" @confirm="confirmOrgan"	@cancel="cancelOrgan">
					</u-select>
				</view>
			</u-form-item>
			<u-form-item label="责任人:" prop="principal" label-width="130"><u-input v-model="form.principal" placeholder="请填写责任人" input-align="right"/></u-form-item>
			<u-form-item label="存放地点:" prop="address" label-width="130"><u-input v-model="form.address" placeholder="请填写存放地点" input-align="right"/></u-form-item>
            <u-form-item label="IP:" label-width="130"><u-input v-model="form.register_ip" placeholder="如有,请填写IP地址" input-align="right"/></u-form-item>
			<u-form-item label="状态:" label-width="130">
				<u-input v-model="form.status" type="select" :border="border" @click="show = true" input-align="right"/>
				<u-action-sheet :list="actionSheetList" v-model="show" @click="actionSheetCallback"></u-action-sheet>
			</u-form-item>
			<u-form-item label="备注:" label-width="130"><u-input v-model="form.comment" placeholder="备注信息" input-align="right"/></u-form-item>
			<view style="margin-top: 20rpx;">
				<u-button type="primary" shape="square" @click="add()">提交</u-button>
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
	      	status: '正常',
	      	typename: '',
			brand:"",
			model:"",
			sn:"",
			organ:"",
			principal:"",
			address:"",
			register_ip:"",
			comment:""
	      },
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
				principal: [
					{ 
						required: true, 
						message: '请填写设备管理责任人', 
						// 可以单个或者同时写两个触发验证方式 
						trigger: ['change','blur'],
					}
				],
				address:[
					{ 
						required: true, 
						message: '请填写设备保管地址', 
						// 可以单个或者同时写两个触发验证方式 
						trigger: ['change','blur'],
					}
				]
				
		   },
		   result: '请选择',
		   organResult: '请选择',
		   selectOrganShow:false,
		   keya: '',
		   keyaOrgan: '',
		   defaultValue: [1], //默认选项
		   defaultOrganValue: [1], //默认选项
		   list: [{
				lbael: 0,
				value: null
		   }],
		   listOrgan: [{
		   				lbael: 0,
		   				value: null
		   }],
		  show:false,
		  border: false,
		  selectShow: false,
		  actionSheetList: [
				{
					text: '正常'
				},
				{
					text: '故障'
				},
				{
					text: '报废'
				}
		  ],		  
	      data: {},
	      item: {},		  
	    };
	  },
	  watch: {
		keya(val) {
			this.$emit('labelnum', this.keya);
		},
		keyaOrgan(val) {
			this.$emit('labelnumorgan', this.keyaOrgan);
		},
	  },
	  onLoad(options) {
	    that = this;
	    vk = that.vk;
	    that.init(options);
		that.getLightType();//解决第一次点击下拉为空，第二次才有值的问题；
		that.getOrgan();//解决第一次点击下拉为空，第二次才有值的问题；
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
		actionSheetCallback(index) {
			this.form.status = this.actionSheetList[index].text;
		},
		// 注意返回值为一个数组，单列时取数组的第一个元素即可(只有一个元素)
		confirm(e) {
			that.list = [];
			that.result = '';
			that.keya = '';
			that.getLightType();  //解决点击确认后，下拉值空的问题；
			e.map((val, index) => {
				let result = val.label
				that.result += val.label;
				let keya = val.value;
				that.keya += val.value
			})
		},
		cancel(e) {
			
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
		},
		cancelOrgan(e) {
			
		},
		getLightType() {
			vk.callFunction({
			  url: 'client/type/sys/select',
			  title: '请求中...',			  
			  success:function(data){				
			    //let server = res.data.data;
			    let values = data.rows;				
			    for (var i = 0; i < values.length; i++) {
			    	var obj = {
			    		label: values[i].typename,
			    		value: values[i]._id,
			    	};	
					//console.log(obj);
			    	that.list.push(obj);
			    }
			  },
			  fail:function(){
			  	vk.alert('请求失败，请再次操作！');
			  }
			});			
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
			    		value: values[i]._id,
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
	    add() {
		  this.$refs.uForm.validate(valid => {
		  	if (valid) {
		  		console.log('验证通过');
				const {status,brand,model,sn,principal,address,register_ip,comment} = that.form;
				var add_data = that.form;
							  add_data.typename = that.result;
							  add_data.organ = that.organResult;
				vk.callFunction({
				  url: 'client/device/sys/add',
							data: add_data,
				  title: '请求中...',
				  success(data) {
				    uni.showToast({
				        title: '添加成功',
				        duration: 2000,
								  success() {
									 setTimeout(function() {
									 that.item = data;
									 that.pageTo("../device/device_list");
									 }, 2000);					  	 
								  }
				    });	          
				  }
				});
		  	} else {
		  		console.log('验证失败');
		  	}
		  });		  
	    }
	  }
	}
</script>
<style lang="scss" src="@/static/styles/common_form.scss">

</style>