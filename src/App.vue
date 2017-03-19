<template>
  <div>
		<vheader :seller="seller"></vheader>
		
		<div class="tab border-1px">
			<div class="tab-item">
				<a v-link="{path:'/goods'}">商品</a></div>
			<div class="tab-item"><a v-link="{path:'/ratings'}">评论</a></div>
			<div class="tab-item"><a v-link="{path:'/sellers'}">商家</a></div>
		</div>
	<router-view :seller="seller" keep-alive></router-view>
  </div>
</template>

<script type="text/ecmascript-6">
	import header from './components/header/header.vue'
	import {urlParse} from 'common/js/url'
	
	const NOERR = 0;
	export default{
		data() {
			return {
				seller: {
					id: (() => {
						let queryParam = urlParse();
						console.log(queryParam)
						return queryParam.id;
					})()
				}
			};
		},
		created() {
			this.$http.get('/api/seller?id=' + this.seller.id).then((res) => {
				res = res.body;
				if(res.errno === NOERR){
					this.seller = Object.assign({}, this.seller, res.data);
				}

			});
		},
		components: {
			vheader : header
		}
	};
</script>

<style lang="stylus" rel='stylesheet/stylus'>
		@import "./common/stylus/mixin.styl"
		
		.tab
			display: flex
			width: 100%
			height: 40px
			line-height: 40px
			border-1px(rgba(7,17,27,0.1))
			.tab-item
				flex:1
				text-align: center
				& > a
					display: block
					font-size: 14px
					line-height: 40px
					color: rgb(77,85,93)
					&.active
						color: rgb(240,20,20)
				
</style>
