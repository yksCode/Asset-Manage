<template>
  <view class="container">
  <!--无内容时 -->
  <view v-if="data.list.length == 0" style="padding: 40% 0 80% 0;">
	 <u-empty text="暂无内容" mode="list"></u-empty>
	 <view class="scrollBox img-center" style="bottom: 8%;right:12%;" @tap="pageTo('../device/device_add')"><image src="../../static/images/add.png" mode="aspectFit" style="width: 34rpx;height: 34rpx;"></image></view>
  </view>
    <view class="content" v-if=" data.list.length > 0 ">
      <view class="item" v-for="(item, index) in data.list" :key="item._id">
       <view class="content-list">
          <view class="information">          
            <view class="st-title">
              <view class="title">用户ID-{{ item._id }}</view>
              <view class="description">用户名：{{ item.username }}</view>
              <view class="time">昵称：{{ item.nickname }}</view>
              <view class="time"><text space="ensp">角色：</text>{{ item. role}}</view>
			  <view class="time" v-if=" item.allow_login_background==true "><text space="ensp">后台权限：</text>  允许登录后台
			  </view>
			  <view class="time" v-else><text space="ensp">后台权限：</text>  没有后台权限 </view>
			  <!--<view class="time"><text space="ensp">手机号：</text>{{ item.mobile }}</view>-->               
            </view>
          </view>
         <u-modal v-model="show" :content="content" :show-cancel-button="true" @cancel="gotoCancel" @confirm="gotoConfirm"></u-modal>
		 <view v-if=" item.role=='admin' " v-show="false">
			 <u-button shape="square" size="mini" :type="item.status==1?'success':'primary' " @click="goEdit(item)">
			   {{ item.status==1?'处理':'编辑' }}
			 </u-button>
		 </view>
		 <view class="time" v-else v-show="true">
			 <u-button shape="square" size="mini" :type="item.status==1?'success':'primary' " @click="goEdit(item)">
			   {{ item.status==1?'处理':'编辑' }}
			 </u-button>
		 </view>		 
        </view>        
      </view>
      
      <u-loadmore :status="data.loadmore" bg-color="#f8f8f8" margin-bottom="30" @loadmore="nextPage" />
    </view>   
    <!--<view class="scrollBox img-center" style="bottom: 8%;right:12%;" @tap="pageTo('../device/device_add')">
        <image src="../../static/images/add.png" mode="aspectFit" style="width: 34rpx;height: 34rpx;"></image>
    </view>-->     
  </view>

</template>

<script>
	var that;		// 当前页面对象
	var vk;			// vk依赖
	export default {
		data() {
			// 页面数据变量
			return {
				show: false,
				content: '确认要更改状态么？',
				typeid:"",
				typestatus:"",
				url: "client/person/sys/getList", // 获取list数据的云函数请求路径
				// init请求返回的数据
				data: {
					list: [], // 列表数据
					pageKey: true, // 是否还能加载下一页
					loadmore: "loading",					
				},
				// 表单请求数据
				form1: {
					addTime: "",
					endTime: "",
					searchvalue: "",
					pageIndex: 1, //当前页码
					pageSize: 10 //每页显示数量
				},
				scrollTop: 0
			};
		},
		onPageScroll(e) {
			that.scrollTop = e.scrollTop;
		},
		// 监听 - 页面每次【加载时】执行(如：前进)
		onLoad(options = {}) {
			that = this;
			vk = that.vk;
			that.options = options;
			that.init(options);
		},
		// 监听 - 页面【首次渲染完成时】执行。注意如果渲染速度快，会在页面进入动画完成前触发
		onReady() {},
		// 监听 - 页面每次【显示时】执行(如：前进和返回) (页面每次出现在屏幕上都触发，包括从下级页面点返回露出当前页面)
		onShow() {},
		// 监听 - 页面每次【隐藏时】执行(如：返回)
		onHide() {},
		// 监听 - 页面下拉刷新
		onPullDownRefresh() {
			setTimeout(function() {
				uni.stopPullDownRefresh();
			}, 1000);
		},
		// 监听 - 页面触底部
		onReachBottom(options) {
			that.nextPage();
		},
		// 监听 - 窗口尺寸变化(仅限:App、微信小程序)
		onResize() {
			
		},
		// 监听 - 点击右上角转发时
		onShareAppMessage(options) {
			
		},
		// 函数
		methods: {
			// 页面数据初始化函数
			init(options) {
				console.log("init: ", options);
				that.getList({
					success: function() {}
				});
			},
			pageTo(path) {
				vk.navigateTo(path);
			},
			// 查询list数据
			getList(obj = {}) {
				vk.pubfn.getListData({
					that: that,
					url: that.url,
					success: obj.success
				});
			},
			// 加载下一页数据
			nextPage() {
				if (that.data.loadmore == "loadmore") {
					that.data.loadmore = "loading";
					that.form1.pageIndex++;
					that.getList();
				}
			},
			goDetail(item) {			  
			  vk.navigateTo({
			    url: '../device/device_info?_id='+item._id  //跳转传参写法
			  });
			},
			goEdit(item) {
			  vk.navigateTo({
			    url: '../person/person_edit?_id='+item._id  //跳转传参写法
			  });
			},
			// 搜索
			onSearch(e) {
				console.log("搜索", that.form1.searchvalue);
				that.form1.pageIndex = 1;
				that.data.pageKey = true;
				that.getList();
			},
			getFirstId() {
			  let id;
			  if (that.data && that.data.rows[0] && that.data.rows[0]._id) {
			    id = that.data.rows[0]._id;
			  }
			  return id;
			},
			findById() {
			  let data = {
			    _id: this.typeid
			  };
			  vk.callFunction({
			    url: 'client/type/sys/findById',
			    title: '请求中...',
			    data: data,
			    success(data) {			      
				  that.typestatus = data.item.status;
				  console.log(that.typestatus);			      
			    }
			  });
			},
			updateById() {
			  let data = {
			    _id: this.typeid,
				status:that.typestatus
			  };
			  console.log("id是"+data)
			  vk.callFunction({
			    url: 'client/type/sys/updateById',
			    title: '请求中...',
			    data: data,
			    success(data) {
			      uni.showToast({
			          title: '更新成功',
			          duration: 2000,
					  success() {
						 setTimeout(function() {
						 that.typestatus = data.info.status;						 
						 that.pageTo("../device/device_list");
						 }, 2000);					  	 
					  }
			      });			      
			    }
			  });
			},
			findByWhereJson() {
			  let data = {
			    _id: that.getFirstId()
			  };
			  vk.callFunction({
			    url: 'template/db_api/pub/findById',
			    title: '请求中...',
			    data: data,
			    success(data) {
			      vk.alert(JSON.stringify(data));
			      that.item = data;
			    }
			  });
			},
			// 列的点击事件
			showModal(e) {				
				this.typeid = e;
				console.log(this.typeid);
				that.findById();
				this.show = true;				
			},
			gotoCancel() {
				console.log("取消变更");				
			},
			gotoConfirm() {
				that.updateById();
			}
		},
		// 计算属性
		computed: {}
	};
</script>
<style lang="scss" src="@/static/styles/audit.scss">

</style>