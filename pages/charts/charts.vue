<template>
	<view class="container">		
		<qiun-title-bar title="设备盘点统计"/>
		<view class="charts-box" style="margin: 20rpx;">
		  <qiun-data-charts type="pie" :chartData="chartsDataPie1"/>
		</view>		
		<qiun-title-bar title="设备常见故障"/>
		<view class="charts-box">
		  <qiun-data-charts type="word" :chartData="chartsDataWord1"/>
		</view>
	</view>
</template>

<script>
	var that;		// 当前页面对象
	var vk;			// vk依赖
	export default {
		data() {
			return {
				chartsDataPie1:{
					"series": [{
					  "name": "",
					  "data": ''
					}]
				},
				chartsDataWord1:{
					"series": [{
						"name": "",
						"textSize": ''
						}]
				},
			}
		},
		onLoad(){
			that = this;
			vk = that.vk;
			that.getReport();			
		},
		onReady() {
			that.getWords();
		},
		methods: {
			pageTo(path) {
				vk.navigateTo(path);
			},
			getReport() {
			  let data = {
			   
			  };			 
			  vk.callFunction({
			    url: 'client/charts/sys/getReport',
			    title: '请求中...',
			    data: data,
			    success(data) {
			       let dataArr = data.rows;
				   let kvlist = []  //用来存放组装后新的数组对象
			       let pie = {}    //添加的对象
			       for(var i in dataArr) {
			         pie = dataArr[i]
					 if(dataArr[i]['_id'] == 0){
						 pie['name'] = '已盘点'
					 }else{
						 pie['name'] = '未盘点'
					 }			        
			         pie['data'] = dataArr[i]['count'];			        		  
			         kvlist.push(pie);							   
			       }
				   that.chartsDataPie1.series = kvlist;
			    }
			  });
			},
			getWords() {
			  let data = {
			   
			  };			 
			  vk.callFunction({
			    url: 'client/charts/sys/getWord',
			    title: '请求中...',
			    data: data,
			    success(data) {
			       console.log(data)
				   let dataArr1 = data.rows;
				   let wordlist = []  //用来存放组装后新的数组对象
				   let word = {}    //添加的对象
				   for(var i in dataArr1) {
				     word = dataArr1[i]	;				 
					 word['name'] = dataArr1[i]['_id']	;				 		        
				     word['textSize'] = vk.pubfn.random(2);			        		  
				     wordlist.push(word);							   
				   }				   
				   that.chartsDataWord1.series = wordlist;
			    }
			  });
			},
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
