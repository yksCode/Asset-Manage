<template>
  <view class="container">
  <!--无内容时 -->
  <view v-if="data.list.length == 0" style="padding: 40% 0 80% 0;">
	 <u-empty text="暂无内容" mode="list"></u-empty>
	 <view class="scrollBox img-center" style="bottom: 8%;right:12%;" @tap="pageTo('../device/device_add')"><image src="../../static/images/add.png" mode="aspectFit" style="width: 34rpx;height: 34rpx;"></image></view>
  </view>
    <view class="content" v-if=" data.list.length > 0 ">
      <view class="item" v-for="(item, index) in data.list" :key="item._id">
       <view class="content-list" @tap="goDetail(item)">
          <view class="information">          
            <view class="st-title">
              <view class="title">ID-{{ item._id }}</view>
              <view class="description">设备类别：{{ item.typename }}</view>
              <view class="time">品牌：{{ item.brand }}</view>
              <view class="time"><text space="ensp">型号：</text>{{ item.model }}</view>
			  <view class="time"><text space="ensp">序列号：</text>{{ item.sn }}</view>
			  <view class="time"><text space="ensp">使用单位：</text>{{ item.organ }}</view>
			  <view class="time"><text space="ensp">故障原因：</text>{{ item.reason }}</view>
              <view class="time">报修时间：{{ item._add_time_str }}</view>
			  <view class="time">报修人：{{ item.reportman }}</view>                  
			  <view class="description" v-if=" item.status=='正常' ">
				  <text space="ensp" style="color: #19be6b;">设备状态：{{ item.status }}</text>
			  </view>
			  <view class="description" v-else>
			  	  <text space="ensp" style="color: #fa3534;">状态：{{ item.status }}</text>
			  </view>
            </view>
          </view>
         <u-modal v-model="show" :content="content" :show-cancel-button="true" @cancel="gotoCancel" @confirm="gotoConfirm"></u-modal>
         <u-button shape="square" size="mini" :type="item.status=='已处理'?'success':'primary' " @click="showModal(item)">
           {{ item.status=='未处理'?'处理':'已处理' }}
         </u-button>
        </view>        
      </view>
      
      <u-loadmore :status="data.loadmore" bg-color="#f8f8f8" margin-bottom="30" @loadmore="nextPage" />
    </view>    
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
				content: '确认设备已维修完成？',
				typeid:"",				
				typestatus:"",
				dev_id:'',
				url: "client/device/kh/getRepaireList", // 获取list数据的云函数请求路径
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
			    url: '../device/repaire_info?_id='+item._id  //跳转传参写法
			  });
			},
			goEdit(item) {
			  vk.navigateTo({
			    url: '../device/device_edit?_id='+item._id  //跳转传参写法
			  });
			},
			// 搜索
			onSearch(e) {
				console.log("搜索", that.form1.searchvalue);
				that.form1.pageIndex = 1;
				that.data.pageKey = true;
				that.getList();
			},	
			updateStatusById() {
			  let data = {
			    _id: this.dev_id,
				status:'正常'
			  };			 
			  vk.callFunction({
			    url: 'client/device/sys/updateStatusById',
			    title: '请求中...',
			    data: data,
			    success(data) {
			       console.log(data)
			    }
			  });
			},
			updateById() {
			  let data = {
			    _id: this.typeid,
				//status:that.typestatus
			  };
			  console.log("id是"+data)
			  vk.callFunction({
			    url: 'client/device/sys/updateRepaireById',
			    title: '请求中...',
			    data: data,
			    success(data) {
				  that.updateStatusById();
			      uni.showToast({
			          title: '处理成功',
			          duration: 1000,
					  success() {
						 setTimeout(function() {						 					 
						 that.pageTo("../home/home");
						 }, 1000);					  	 
					  }
			      });			      
			    }
			  });
			},			
			// 列的点击事件
			showModal(e) {				
				if(e.status=="已处理"){
					uni.showToast({
					    title: '故障已处理',
					    duration: 2000,
						icon: 'error'
					});					
				}else{
					this.typeid = e._id;
					this.dev_id = e.dev_id;
					console.log(this.typeid);
					//that.findById();
					this.show = true;
				}								
			},
			gotoCancel() {
				console.log("取消处理");				
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