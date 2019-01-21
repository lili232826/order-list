<script>
	export default {
		onLaunch: function () {
			//console.log('App Launch')
		},
		onShow: function () {
			//console.log('App Show')
		},
		onHide: function () {
			//console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
	.uni-flex{
		/* 弹性盒默认 */	
		display: -webkit-flex;
		display: flex; 
		display: box;
		-webkit-align-items:center;
		 -moz-align-items:center;
		-ms-align-items:center;
		 align-items:center;
	}
	.uni-scroll-view{
		position:static !important;
	}
		/* 购物车的弹球 */
	 .outer{
	 		width: 16px;
	 		height: 16px;
	 		position: absolute;
	 		left: 0px;
	 		top: 0px;
	 		transition: all 0.35s linear;
	 		z-index: 90;
	 }
	 .outer .inner{
	 		width: 16px;
	 		height: 16px;
	 		background: orangered;
	 		border-radius: 50%;
	 		transition: all 0.35s cubic-bezier(0.39,-0.4,0.83,0.23);
	 		position: absolute;
	 		z-index: 999999;
	 }
</style>
