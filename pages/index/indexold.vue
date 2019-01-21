<template>
	<view>
       <div class="mui-content hg-content">
			<div class="hg-menu  mui-col-xs-3 " style="background:#efedee">
				<div class="" style="height: 100%;">
					<!-- <div class="swiper-slide slidescroll" id="segmentedControls">
						 <a v-for="(item,index) in menuList" :key="index" :class="{active: curIndex === index}">
							{{item.name}}
						 </a>
					</div> -->
					<scroll-view scroll-y style="height: 100%;">
						<view v-for="(item,index) in menuList" :data-id="item.id" :class="{active: curIndex == index}" @tap="tapTo(index,item.id)" style="position: relative;">
							<text class="slide">{{item.name}}</text><span class="mui-badge mui-badge-danger" v-if="item.typeNum>0">{{item.typeNum}}</span>
						</view>
					</scroll-view>
				</div>
			</div>
			<div class="hg-list mui-col-xs-9">
				<div class="" id="segmentedControlContents" style="height: 100%;" ref="rightWrapper">
					<scroll-view scroll-y style="height: 100%;" :scroll-with-animation='true'  :scroll-into-view="toView" @scrolltoupper="upper" @scroll="scroll">
						<view v-for="(item,index) in menuList" :id="item.id" class="list">
							<ul class="mui-table-view">
								<h4>{{item.name}}</h4>
								<li v-for="(list,ind) in item.menu" :data-id="list.id" class="mui-table-view-cell">
									<a href="javascript:;">
										<img class="mui-media-object mui-pull-left" :src="'/h5/static'+list.pic">
										<div class="mui-media-body">
											{{list.name}}{{index}}
											<p class='mui-ellipsis'>￥<span>{{list.price}}</span></p>
											<div class='mui-ellipsis checkbox'>
												<span>
													<button class="check-reduce" v-if="list.chartNum>0" @tap="listjian(list.id)"></button>
												</span>
												<span class="num" v-if="list.chartNum>0">{{list.chartNum}}</span>
												<span>
													<button class="check-add" @tap="listAdd(list.id)"></button>
												</span>
											</div>
										</div>
									</a>
								</li>
							</ul>
						</view>
					</scroll-view>
				</div>
				<!-- <div class="mui-loading" style="margin-top:26vh">
					<div class="mui-spinner">
					</div>
				</div> -->
			</div>
       </div>
	   <footer class="cart-foot">
	   	<div class="cart-wrap">
	   		<div class="cart" :class="allNum>0 ? 'active' : ' ' " @tap="showList()">
	   			<span class="mui-badge mui-badge-danger" v-if="allNum>0">{{allNum}}</span>
	   		</div>
	   		<div class="price">
	   			<div>
	   				￥<span class="num">{{allSum}}</span>
	   			</div>
	   		</div>
	   		<div class="chooseEnd" :class="allNum>0 ? 'active' : ' ' " @tap="toOrder">
	   				选好了
	   		</div>
	   		<div id="checked-all">
				<div class="checked-list-wrap mui-content" v-if="fleg">
					<div class="top-bar"><span>已选商品</span><span @tap="clearShop"><i class="mui-icon mui-icon-trash"></i>清空购物车</span></div>
					<div class="checked-list">
						<ul class="mui-table-view">
							<li class="mui-table-view-cell" v-for="list in checkedList">
								<div class="list">
									<div class="name"><div>{{list.name}}</div></div>
									<div class="price-checked"><span>￥{{list.price*list.chartNum}}</span></div>
									<div class='mui-ellipsis checkbox'><span><button class="check-reduce" @tap="listjian(list.id)"></button></span><span>{{list.chartNum}}</span><span><button class="check-add"  @tap="listAdd" v-bind:id="list.id"></button></span></div>
								</div>
							</li>
						</ul>	
					</div>
				</div>
	   		</div>
	   	</div>
	   </footer>
	   <div class="mui-backdrop" v-if="fleg" @tap="fleg=false"></div><!-- 蒙层 -->
    </view>
</template>

<script>
import {mapState} from 'vuex';
var rightInterval=25;//切换之间的间距，小标题的高度
	export default {
		data() {
			return {
				curIndex:0,
				toView:"",
				menuList : [],
				allNum:0,
				allSum:0,
				fleg:false
			}
		},
		onLoad() {
			console.log(uni.getStorageSync("menuList"),"oo")
			if(uni.getStorageSync("menuList") && this.hasLogin){
				this.menuList=uni.getStorageSync("menuList");
				for (let i=0;i<this.menuList.length;i++){
					let item=this.menuList[i];
					if(item["typeNum"]>0){
						this.allNum+=item["typeNum"];
						for(let j=0;j<item["menu"].length;j++){
							let list=item["menu"][j];
							if(list["chartNum"]>0){
								this.allSum+=list["price"]*list["chartNum"];
							}
						}
					}
				}
				return;
			}
			this.getMenuList();
			
		},
		methods: {
			getMenuList:function(){
				var Vue=this;
				uni.request({
					url: 'https://www.easy-mock.com/mock/5c27219996d1fb1e7c95e78c/hg/list',
					method: 'GET',
					data: {},
					success: res => {
						var newsList = res.data.data;
						this.menuList = newsList;
						this.toView=this.menuList[0]['id'];
						
						uni.hideLoading();
					},
					complete: res => {
						uni.hideLoading();
						uni.stopPullDownRefresh();
					}
				});
			},
			upper:function(e){
				/* console.log(e,"upper") */
			},
			scroll:function(e){
				var sLong=e.target.scrollTop//滚动的距离；
				var sAll=e.target.scrollHeight//所有列表的高度；
				var rightWrapper=this.$refs.rightWrapper;
				var slides=rightWrapper.getElementsByClassName("list");
				//var slides=this.$refs.rightWrapper.children
				for(var i=0;i<slides.length;i++){
					if(sLong>slides[i].offsetTop-rightInterval && sLong<slides[i].offsetTop + slides[i].offsetHeight-rightInterval){
						this.curIndex=i;
					}
				}
			},
			tapTo:function(ind,id){
				this.curIndex=ind;
				this.toView = id;
				console.log(id)
				console.log(id,"id")
			},
			showList:function(){
				if(this.allNum<=0){
					return false;
				};
				this.fleg=!this.fleg;
				console.log(this.fleg)
			},
			listAdd:function(e){
				console.log(e.target,"htht")
				var id=e.target.id;
				
				let Vue=this;
				for (let i=0;i<this.menuList.length;i++){
					let item=this.menuList[i];
					for(let j=0;j<item["menu"].length;j++){
						let list=item["menu"][j];
						if(list["id"]==id){
							//console.log(id,"ididididididid")
							list["chartNum"]+=1;
							item["typeNum"]+=1;
							Vue.allSum+=list["price"]*1;
							Vue.allNum+=1;
							uni.setStorageSync("menuList",Vue.menuList)
							break;
						}
					}
				}
			},
			listjian:function(id){
				let Vue=this;
				
				for (let i=0;i<this.menuList.length;i++){
					let item=this.menuList[i];
					if(item["typeNum"]>0){
						for(let j=0;j<item["menu"].length;j++){
							let list=item["menu"][j];
							if(list["id"]==id){
								console.log(id,"ididididididid")
								list["chartNum"]-=1;
								item["typeNum"]-=1;
								Vue.allSum-=list["price"]*1;
								Vue.allNum-=1;
								uni.setStorageSync("menuList",Vue.menuList)
								break;
							}
						}
					}
				}
			},
			clearShop:function(){
				this.menuList.forEach(function(item){
					item["typeNum"]=0;
					item["menu"].forEach(function(list){
						list["chartNum"]=0;
					})
				});
				this.fleg=false;
				this.allNum=0;
				this.allSum=0;
			},
			toOrder:function(){
				if(this.allNum<=0){
					return false;
				};
				console.log(this.hasLogin)
				if (!this.hasLogin) {
				    uni.showModal({
				        title: '未登录',
				        content: '您未登录，需要登录后才能继续',
				        /**
				         * 如果需要强制登录，不显示取消按钮
				         */
				        showCancel: !this.forcedLogin,
				        success: (res) => {
				            if (res.confirm) {
								/**
								 * 如果需要强制登录，使用reLaunch方式
								 */
				                if (this.forcedLogin) {
				                    uni.reLaunch({
				                        url: '../login/login'
				                    });
				                } else {
				                    uni.navigateTo({
				                        url: '../login/login'
				                    });
				                }
				            }
				        }
				    });
				}else{
					 uni.navigateTo({
					    url: '../order/order'
					});
				}
			}
		},
		computed:{
			...mapState(['forcedLogin', 'hasLogin', 'userName']),
			checkedList(){
				let Total=[];
				this.menuList.forEach(function(item){
					if(item["typeNum"]>0){
						item["menu"].forEach(function(list){
							if(list["chartNum"]>0){
								Total.push(list);
							}
						});
					}
				});
				
				console.log(Total)
				return Total;
			},
			typeNumTotal(menu){
				let Total=0;
				menu.map((item) => {total += item.chartNum})
				return total;
			},
			
		}
	}
</script>

<style>
uni-button:after{
	border: 0;
}
.mui-badge {
    font-size: 12px;
    line-height: 1;
    display: inline-block;
    padding: 3px 6px;
    color: #333;
    border-radius: 100px;
    background-color: rgba(0,0,0,.15);
}
.mui-col-xs-3{
	width: 25%;
	float: left;
	height: 100%;
}
.mui-col-xs-9{
	width: 75%;
	float: right;
	height: 100%;
}
.mui-table-view {
position: relative;
margin-top: 0;
margin-bottom: 0;
padding-left: 0;
list-style: none;
background-color: #fff;
}
.mui-table-view .mui-media-object {
    width: 84px;
    height: 84px;
    max-width: 84px;
    border-radius: 4px;
}
.mui-table-view-cell {
    position: relative;
    overflow: hidden;
    padding: 11px 15px;
    -webkit-touch-callout: none;
}
.mui-table-view .mui-media-body {
    padding: 14px 0;
    font-size: 16px;
	
}
.mui-table-view .mui-media-object.mui-pull-left {
    margin-right: 10px;
}
.mui-table-view-cell>a:not(.mui-btn) {
    position: relative;
    display: flex;
    overflow: hidden;
    margin: -11px -15px;
    padding: inherit;
    white-space: nowrap;
    text-overflow: ellipsis;
    color: inherit;
}
	.color-tip{
		color:#ff6600;
	}
	/* .mui-row.mui-fullscreen>[class*="mui-col-"] {
		height: 100%;
	} */
	.mui-col-xs-3,
	.mui-control-content {
		overflow-y: auto;
		height: 100%;
	}
	.mui-backdrop{
		background-color: rgba(0,0,0,.6);
	}
	/* .mui-segmented-control {
		overflow: visible;
	} */
	.hg-menu .slide{
		line-height: 50px;
		width: 100%;
		border: none;
		font-size: 14px;
		font-weight: 400;
		color: #000;
		text-align: center;
		display: inline-block;
	}
	.hg-menu .active text{
		background-color: #fff;
		border: none;
		color: #ff450e;
	}
	.hg-menu .mui-badge-danger,.cart-foot .mui-badge-danger{
		position: absolute;
		top:4px;
		right: 4px;
		transform: scale(0.84);
		color: #fff;
		background-color: red;
	}
	.cart-foot .mui-badge-danger{
		top:-4px;
		right: -2px;
	}
	.mui-segmented-control.mui-segmented-control-inverted.mui-segmented-control-vertical .mui-control-item,
	.mui-segmented-control.mui-segmented-control-inverted.mui-segmented-control-vertical .mui-control-item.mui-active{
		border:none;
	}
	.mui-table-view:after{
		height: 0;
	}
	.mui-title{
		color: #fff;
	}
	.mui-control-content{
		background-color:#fff;
		/* padding:14px; */
	}
	.logo-pic{
					width:100%;
				}
	.active-tip{
					text-align: center;
				}
	.mui-bar-nav{
		position: fixed;
	    z-index: 10;
	    right: 0;
	    left: 0;
	    height: 44px;
	    padding-right: 10px;
	    padding-left: 10px;
		background:#fe8800;
		background:-webkit-gradient(linear, 0 50%, 100% 50%, from(#fe8800), to(#ff6600));
	    background:-moz-linear-gradient(left, #fe8800,#ff6600);  
	    filter:progid:DXImageTransform.Microsoft.gradient(startColorstr=#fe8800,endColorstr=#ff6600,grandientType=1);   
	}	
	#segmentedControlContents .mui-table-view{
		padding-top: 10px;
	}
	.mui-table-view .mui-media-body{
		padding: 14px 0;
		font-size:16px;
	}
	#segmentedControlContents .mui-table-view-cell p{
		margin-top: 20px;
		color: #ff460e;
		font-size: 12px;
	}
	.mui-table-view-cell p>span{
		font-size:18px
	}
	.mui-table-view-cell p>span:nth-child(2){
		margin-left:10px;
	}
	/* list */
	.list_bg{
		position: absolute;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		padding-top:44px;
		background-size: cover;
		box-sizing: border-box;
		overflow:hidden;
	}
	.ph_input::-webkit-input-placeholder{
	    color:#a7a7a7;
	}
	.search_input:-moz-placeholder{
	    color:#a7a7a7;
	}
	.search_input::-moz-placeholder{
	    color:#a7a7a7;
	}
	.search_input:-ms-input-placeholder{
	    color:#a7a7a7;
	}
	#segmentedControlContents .mui-table-view-cell:after,.mui-table-view:before{
		height: 0;
	}
	#segmentedControlContents .mui-table-view .mui-media-object{
		width: 84px;
		height: 84px;
		max-width: 84px;
		border-radius: 4px;
	}
	.featured{
		background-color: transparent;
		background-image: none;
	}
	.hg-content{
		    position: absolute;
			top: 0;
			right: 0;
			bottom: 0;
			left: 0;
			padding-bottom: 50px;
			/* overflow: hidden; */
	}
	.hg-content .slidescroll,.hg-content .slidescroll{
		height: auto;
	}
	.hg-content .checkbox{
		position: absolute;
		right: 2px;
		bottom: 15px;
		height: 25px;
	}
	 .checkbox>span{
		padding: 10px 4px;
	}
	 .checkbox>span.num{
		margin-top: 4px;
	}
	 .checkbox button{
		border-radius: 50%;
		display: inline-block;
		height: 28px;
		width: 25px;
		background-size: 100%;
		background-repeat: no-repeat;
		border: none;
	}
	.checkbox .check-reduce{
		background-image: url(../../static/images/icon/btn-reduce.png);
	}
	 .checkbox .check-add{
		background-image: url(../../static/images/icon/btn-add.png);
	}
	.cart-foot{
		position: fixed;
		z-index: 999;
		right: 0;
		left: 0;
		bottom: 0;
		height: 50px;
		background-color: #333333;
	} 
	.cart-wrap{
		width: 100%;
		height: 100%;
		display: -webkit-flex;
		display: flex; 
		display: box;
		position: relative;
	}
	.cart-wrap .cart{
		width: 50px;
		height: 50px;
		position: relative;
		top: -10px;
		margin: 0 10px;
		flex-shrink: 0;
		background-image: url(../../static/images/icon/cart-no-icon.png);
		background-size: contain;
		background-repeat: no-repeat;
		z-index: 1001;
	} 
	.cart-wrap .cart.active{
		background-image: url(../../static/images/icon/cart-icon.png);
	}
	.cart-wrap .price{
		-webkit-flex: 1 1 auto;
	  -ms-flex: 1 1 auto;
	  flex: 1 1 auto;
		color: #999;
		font-size: 18px;
		padding-top:10px
	}
	.cart-wrap .price span.num{
		font-size: 24px;
	}
	.cart-wrap .chooseEnd{
		color: #fff;
		font-size: 16px;
		height: 50px;
		line-height: 50px;
		background: #999;
		flex-shrink: 0;
		padding: 0 30px;
	}
	.cart-wrap .chooseEnd.active{
		background:#fe8800;
		background:-webkit-gradient(linear, 0 50%, 100% 50%, from(#fe8800), to(#ff6600));
		background:-moz-linear-gradient(left, #fe8800,#ff6600);  
		filter:progid:DXImageTransform.Microsoft.gradient(startColorstr=#fe8800,endColorstr=#ff6600,grandientType=1);
	} 
	/* 购物车的弹球 */
	 .boll-wrap .outer{
	 		width: 16px;
	 		height: 16px;
	 		position: absolute;
	 		left: 0px;
	 		top: 0px;
	 		transition: all 0.35s linear;
	 		z-index: 999999;
	 }
	 .boll-wrap .outer .inner{
	 		width: 16px;
	 		height: 16px;
	 		background: orangered;
	 		border-radius: 50%;
	 		transition: all 0.35s cubic-bezier(0.39,-0.4,0.83,0.23);
	 		position: absolute;
	 		z-index: 999999;
	 }
	 .boll-wrap .outer.point-pre{
	 		display: none;
	 } 
	/* 购物车的已选列表 */
	.cart-wrap .checked-list-wrap{
		position: absolute;
		right: 0;
		left: 0;
		bottom: 50px;
		background-color: #fff;
		z-index: 1000;
	}
	.checked-list-wrap.hidden{
		display: none;
	}
	.checked-list{
		height: 240px;
		overflow-y: scroll;
	}
	.checked-list .list{
		display: -webkit-flex;
	    display: -ms-flexbox;
	    display: flex;
	    -webkit-align-items: center;
	    -ms-flex-align: center;
	    align-items: center;
	}
	.checked-list .list>div.name{
		-webkit-flex: 1 1 auto;
	    -ms-flex: 1 1 auto;
	    flex: 1 1 auto;
	}
	.checked-list .list>div.price-checked{
		padding-right: 40px;
	}
	.checked-list .list>div.price-checked,.checked-list .list>div.checkbox{
		-webkit-flex-shrink: 0;
	    -ms-flex-negative: 0;
	    flex-shrink: 0;
	}
	.checked-list-wrap .top-bar{
		line-height: 30px;
	    height: 42px;
	    background: #F4F4F4;
	    padding: 6px 15px;
	}
	.checked-list-wrap .top-bar span:nth-child(1){
		float: left;
	}
	.checked-list-wrap .top-bar span:nth-child(2){
		float: right;
	}
.mui-backdrop {
    position: fixed;
    z-index: 998;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(0,0,0,.6);
}
</style>
