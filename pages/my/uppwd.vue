<template>
	<view class="app forget">
		<!-- 页面内容开始 -->

		<view class="content">
			<!-- 主体 -->
			<view class="form-view">
				<view class="tips">注意：仅用于系统管理员，不支持微信登陆用户</view>

				<view class="form-item form-border" style="margin: 20rpx;">
					<!-- 文本框 -->
					<u-input
						class="main-input"
						v-model="form1.oldPassword"
						type="password"
						placeholder="请输入原密码"
						placeholder-style="'color':'#8e8e8e'"
					/>
				</view>

				<view class="form-item form-border" style="margin: 20rpx;">
					<!-- 文本框 -->
					<u-input
						class="main-input"
						v-model="form1.newPassword"
						type="password"
						placeholder="请输入新密码"
						placeholder-style="'color':'#8e8e8e'"
					/>
				</view>
				<view class="form-item form-border" style="margin: 20rpx;">
					<!-- 文本框 -->
					<u-input
						class="main-input"
						v-model="form1.password_confirmation"
						type="password"
						placeholder="请再次输入新密码"
						placeholder-style="'color':'#8e8e8e'"
					/>
				</view>
			</view>
			<view class="login-btn" style="margin: 20rpx;">
				<u-button shape="circle" @click="updatePwd" :plain="false" :hair-line="false" type="warning">修改密码</u-button>
			</view>
		</view>
		<!-- 页面内容结束 -->
	</view>
</template>

<script>
	var that; 										// 当前页面对象
	var vk;												// vk依赖
	export default {
		data() {
			return {
				form1:{
					oldPassword: '',
					newPassword: '',
					password_confirmation:''
				}

			}
		},
		onLoad(options) {
			that = this;
			vk = that.vk;
		},
		methods: {			
			// 修改密码
			updatePwd(){
				var form1 = that.form1;
				vk.userCenter.updatePwd({
					data:{
						oldPassword: form1.oldPassword,
						newPassword: form1.newPassword,
						password_confirmation: form1.password_confirmation
					},
					success:function(data){
						console.log(data);
						vk.alert("修改成功");
						vk.userCenter.logout({
						  success:function(data){
						    uni.reLaunch({
						        url: '../home/home'
						    });									   
						  }
						});
					}
				});
			},
			// 重置密码
			resetPwd(){
				var form1 = that.form1;
				vk.userCenter.resetPwd({
					success:function(data){
						vk.alert("密码已重置为123456");
					}
				});
			}
		}
	}
</script>

<style lang="scss" scoped>
	.content {
		padding: 15px;
	}
	.content input {
		height: 46px;
		border: solid 1px #DDDDDD;
		border-radius: 5px;
		margin-bottom: 15px;
		padding: 0px 15px;
		display: block;
	}
	.content button {
		margin-bottom: 15px;
	}
	.content navigator {
		display: inline-block;
		color: #007AFF;
		border-bottom: solid 1px #007AFF;
		font-size: 16px;
		line-height: 24px;
		margin-bottom: 15px;
	}
	.tips {
		text-align: center;
		line-height: 20px;
		font-size: 14px;
		color: #999999;
		margin-bottom: 20px;
	}
</style>
