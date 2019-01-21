<template>
    <view>
        <view class="section">
            <view class="section__title">vertical scroll</view>
            <scroll-view scroll-y style="height: 500px;" @scrolltoupper="upper" @scrolltolower="lower" @scroll="scroll" :scroll-into-view="toView" :scroll-top="scrollTop" :scroll-with-animation="true">
                <view v-for="(item,index) in students" :key="index" v-bind:id="item.age" class="view" @click="clickFn">
					<text>{{item.name}}</text>
				</view>
            </scroll-view>

            <view class="btn-area">
                <button size="mini" @click="tap">click me to scroll into view </button>
                <button size="mini" @click="tapMove">click me to scroll</button>
            </view>
        </view>
        <view class="section section_gap">
            <view class="section__title">horizontal scroll</view>
            <scroll-view class="scroll-view_H" scroll-x style="width: 100%">
                <view id="green" class="scroll-view-item_H bc_green"></view>
                <view id="red" class="scroll-view-item_H bc_red"></view>
                <view id="yellow" class="scroll-view-item_H bc_yellow"></view>
                <view id="blue" class="scroll-view-item_H bc_blue"></view>
            </scroll-view>
        </view>
		
			<!-- <view>
				<div style="">
					<div>
						hhhhhh
					</div>
					<text style="display: block;margin-bottom: 100px;" v-for="(item, index) in students" class="persons" @click="menuClick" v-bind:id="index">{{index}} - {{item.name}}</text>
				</div>
				<div style="">
					<text style="display: block;margin-bottom: 100px;" v-for="(item, index) in students" class="persons" @click="menuClick" v-bind:id="index">{{index}} - {{item.name}}</text>
				</div>
			 </view> -->
		
		
    </view>
	
</template>

<script>
	var order = ['red', 'yellow', 'blue', 'green', 'red']
export default {
    data(){
		return{
			 
			    toView: 'red',
			    scrollTop: 100,
				students : [
				   {name : "张三", age : 18},
				   {name : "李四", age : 20},
				    {name : "张三", age : 18},
					{name : "张三1", age : 18},
					{name : "李四1", age : 20},
					 {name : "张三1", age : 18}
				  ]
		}
	},
	
    methods: {
        upper: function (e) {
            //console.log(e)
        },
        lower: function (e) {
            //console.log(e)
        },
        scroll: function (e) {
            console.log(e.target)
        },
        tap: function (e) {
            for (var i = 0; i < order.length; ++i) {
                if (order[i] === this.toView) {
                    this.toView = order[i + 1];
                    break
                }
            }
        },
        tapMove: function (e) {
            this.scrollTop = this.scrollTop + 10
        },
		menuClick : function(e){
			  console.log(e);
			  console.log(e.target.id);
		 },	 
		 clickFn:function(e){
			 console.log(e)
		 }
    }
}
</script>

<style>
	.scroll-view-item{
		height: 150px;
	}
	.bc_green{
		background-color: #2AC845;
	}
	.bc_red{
		background-color: red;
	}
	.bc_yellow{
		background-color: yellow;
	}
	.bc_blue{
		background-color: blue;
	}
	.view{
		border-bottom: 1px solid #ccc;
		height: 150px;
	}
</style>
