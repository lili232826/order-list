<template>
	<view>
		我是订单列表
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		},
		onLaunch: function () {
			//console.log('App Launch')
		},
		onShow: function () {
			console.log(uni.getStorageSync("menuList"))
		},
		onHide: function () {
			//console.log('App Hide')
		},
		onBackPress:function(){
			console.log('onBackPress')
		}
	}
</script>

<style>

</style>
