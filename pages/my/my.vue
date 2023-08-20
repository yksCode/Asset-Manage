<template>
	<view>		
		<view class="u-flex user-box u-p-l-30 u-p-r-20 u-p-b-30">
			<view class="u-m-r-10">
				<u-avatar :src="vk.getVuex('$user.userInfo.avatar')" size="140"></u-avatar>
			</view>
			<view class="u-flex-1">
				<view class="u-font-18 u-p-b-20">{{ vk.getVuex('$user.userInfo.nickname') || vk.getVuex('$user.userInfo.username') }}</view>
				<view class="u-font-14 u-tips-color">角色:{{ vk.getVuex('$user.userInfo.role')}}</view>
			</view>	
			<u-modal v-model="wxshow" :content="contentwx" :show-cancel-button="true" @cancel="gotowxCancel" @confirm="gotowxConfirm"></u-modal>
			<view class="u-m-l-10 u-p-10" v-if="vk.getVuex('$user.userInfo.nickname')" v-show="false">
				<u-button type="primary" shape="square" size="mini" @click="showwxModal()">获取用户信息</u-button>
			</view>
			<view class="u-m-l-10 u-p-10" v-else v-show="true">
				<u-button type="primary" shape="square" size="mini" @click="showwxModal()">获取用户信息</u-button>
			</view>
		</view>		
		
		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item icon="star" title="报修" @tap="pageTo('../my/my_repaire')"></u-cell-item>
				<!--<u-cell-item icon="photo" title="相册"></u-cell-item>-->
				<u-cell-item icon="fingerprint" title="改密" @tap="pageTo('../my/uppwd')"></u-cell-item>
				<u-cell-item icon="heart" title="咨询" :arrow="false">
					<u-button type="primary" open-type="contact" shape="square" size="mini">联系客服</u-button>				
				</u-cell-item>
			</u-cell-group>
		</view>
		
		<view class="u-m-t-20">
			<u-modal v-model="show" :content="content" :show-cancel-button="true" @cancel="gotoCancel" @confirm="gotoConfirm"></u-modal>
			<u-cell-group>				
				<u-cell-item icon="setting" title="退出" @click="showModal()"></u-cell-item>
			</u-cell-group>
		</view>
	</view>
</template>

<script>
	var that;		// 当前页面对象
	var vk;			// vk依赖
	export default {
		data() {
			return {
				show: false,
				content: '确定退出系统？',
				pic:'/static/logo.png',	
				wxshow:false,
				contentwx: '正在请求您的个人信息',
				userInfo:'',
				wxUserInfo:''
			}
		},
		onLoad(){
			that = this;
			vk = that.vk;			
			setTimeout(function(){
				vk.pubfn.checkLogin();
			},0)
		},
		onReady() {
			that.userInfo = vk.getVuex('$user.userInfo');
			console.log(that.userInfo)
		},
		methods: {
			pageTo(path) {
				vk.navigateTo(path);
			},
			logout(){
				vk.userCenter.logout({
				  success:function(data){
				    uni.reLaunch({
				        url: '../home/home'
				    });									   
				  }
				});
			},			
			// 列的点击事件
			showModal(e) {				
			    this.show = true;										
			},
			gotoCancel() {
				console.log("取消处理");				
			},
			gotoConfirm() {
				that.logout();
			},
			showwxModal(e) {
			    this.wxshow = true;										
			},
			gotowxCancel() {
				console.log("取消处理");				
			},
			gotowxConfirm() {
				uni.getUserProfile({
				  desc: "获取你的昵称、头像、地区及性别",
				  success: res => {
				    console.log(res)
				    that.wxUserInfo = res.userInfo;
					that.updateUser();
				  },
				  fail: res => {
				  	 //拒绝授权
				    vk.toast("您拒绝了请求");
				    return;
				  }
				})
			},
			updateUser(){
				vk.userCenter.updateUser({
				  data:{
				    nickname:that.wxUserInfo.nickName,
					avatar:that.wxUserInfo.avatarUrl,
					gender:that.wxUserInfo.gender
				  },
				  success:function(data){
				    // 成功后的逻辑
				    
				  }
				});				
			}
		}
	}
</script>

<style lang="scss">
page{
	background-color: #ededed;
}

.camera{
	width: 54px;
	height: 44px;
	
	&:active{
		background-color: #ededed;
	}
}
.user-box{
	background-color: #fff;
}
</style>
