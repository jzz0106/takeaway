<template>
  <div class="ratings"  v-el:ratings>
		<div class="ratings-content">
			<div class="overview">
				<div class="overview-left">
					<h1 class="score">{{seller.score}}</h1>
					<div class="title">综合评分</div>
					<div class="rank">高于周边商家{{seller.rankRate}}</div>
				</div>
				<div class="overview-right">
					<div class="score-wrapper">
						<span class='title'>服务态度</span>
						<star :size="36" :score="seller.serviceScore"></star>
						<span class="score">{{seller.serviceScore}}</span>
					</div>
					<div class="score-wrapper">
						<span class='title'>商品评分</span>
						<star :size="36" :score="seller.foodScore"></star>
						<span class="score">{{seller.foodScore}}</span>
					</div>
					<div class="delivery-wrapper">
						<span class='title'>送达时间</span>
						<span class="delivery">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
				
			</div>
			<split></split>
			<ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="ratings"></ratingselect>
			<div class="rating-wrapper">
				<ul>
					<li v-for='rating in ratings' v-show="needShow(rating.rateType, rating.text)" class='rating-item'>
						<div class="avatar">
							<img :src="rating.avatar" width="28"/>
						</div>
						<div class="content">
							<h1 class="name">{{rating.username}}</h1>
							<div class="star-wrapper">
								<star :size="24" :score="rating.score"></star>
								<span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
							</div>
							<p class="text">{{rating.text}}</p>
							<div class="recommend" v-show="rating.recommend && rating.recommend.length">
								<span class='icon-thumb_up'></span>
								<span v-for='item in rating.recommend' class="item">{{item}}</span>
							</div>
							<div class="time">
								{{rating.rateTime | formatDate}}
							</div>
						</div>
					</li>
				</ul>
			</div>
		</div>
  </div>
</template>

<script type="text/ecmascript-6">
	import split from 'components/split/split'
	import BScroll from 'better-scroll'
	import ratingselect from 'components/ratingselect/ratingselect'
	import star from 'components/star/star'
	import {formatDate} from 'common/js/date'
	
	const ALL = 2;
	const NOERR = 0;
	export default{
		props: {
			seller: {
				Object
			}
		},
		data() {
			return {
				ratings: [],
				showFlag: false,
				selectType: ALL,
				onlyContent: true
			}
		},
		created() {
			this.$http.get('api/ratings').then((res) => {
				res = res.body;
				if(res.errno === NOERR){
					this.ratings = res.data;
					this.$nextTick(() => {
						this.scroll = new BScroll(this.$els.ratings, {
						click:true
						})
					})
					
				}
			})
		},
		filters: {
			formatDate(time) {
				let date = new Date(time);
				return formatDate(date,'yyyy-MM-dd hh:mm');
			}
		},
		events: {
			'ratingtype.select'(type) {
				this.selectType = type;
				this.$nextTick(() => {
					this.scroll.refresh();
				})		
			},
			'content.toggle'(onlyContent) {
				this.onlyContent = onlyContent;
				this.$nextTick(() => {
					this.scroll.refresh();
				})		
			}
		},
		methods: {
			needShow(type,text) {
				if(this.onlyContent && !text) {
					return false;
				}
				if(this.selectType === ALL) {
					return true;
				}else{
					return type === this.selectType;
				}
			}
		},
		components: {
			star,
			ratingselect,
			split
		}
	};
</script>

<style lang="stylus" rel='stylesheet/stylus'>
	@import "../../common/stylus/mixin";
	.ratings
		position: absolute
		top: 174px
		bottom: 0
		width: 100%
		overflow: hidden
		.overview
			display: flex
			padding: 18px 0
			.overview-left
				flex: 0 0 137px
				width: 137px
				border-right: 1px solid rgba(7,17,27,.1)
				text-align: center
				padding: 6px 0
				@media only screen and (max-width: 350px)
					flex: 0 0 120px
					width: 120px
				.score
					margin-bottom: 6px
					line-height: 28px
					font-size: 24px
					color: rgb(255, 153,0)
				.title
					line-height: 12px
					font-size: 12px
					margin-bottom: 8px
					color: rgb(7,17,27)
				.rank
					line-height: 10px
					font-size: 10px
					color: rgb(147,153,159)
			.overview-right
				flex: 1
				padding: 6px 0 6px 24px
				@media only screen and (max-width: 350px)
					padding-left: 6px
				.score-wrapper
					margin-bottom: 8px
					font-size: 0
					.title
						font-size: 12px
						line-height: 18px
						color: rgb(7,17,27)
					.star
						display: inline-block
						margin: 0 12px
						vertical-align: top
					.score
						line-height: 18px
						display: inline-block
						vertical-align: top
						font-size: 10px
						color: rgb(255,153,0)
				.delivery-wrapper
					font-size: 0
					.title
						font-size: 12px
						line-height: 18px
						color: rgb(7,17,27)
					.delivery
						margin-left: 12px
						font-size: 12px
						color: rgb(147,153,159)
		.rating-wrapper
			padding: 0 18px
			.rating-item
				display: flex
				padding: 18px 0 
				border-1px(rgba(7,17,27,.1))
				.avatar
					flex: 0 0 28px
					width: 28px
					margin-right: 12px
					img
						border-radius: 50%
				.content
					position: relative
					flex: 1
					.name
						margin-bottom: 4px
						line-height: 12px
						font-size: 10px
						color: rgb(7,17,27)
					.star-wrapper
						position: relative
						margin-bottom: 6px
						font-size: 0
						.star
							display: inline-block
							vertical-align: top
							margin-right:	6px
						.delivery
							display: inline-block
							vertical-align: top
							line-height: 12px
							font-size: 10px
							color: rgb(147,153,159)
					.text
						margin-bottom: 8px
						line-height: 18px
						color: rgb(7,17,27)
						font-size: 12px
					.recommend
						font-size: 0
						line-height: 16px
						.icon-thumb_up, .item
							display: inline-block
							margin: 0 8px 4px 0
							font-size: 9px
						.icon-thumb_up
							color: rgb(0, 160, 220)
						.item
							padding: 0 6px
							border: 1px solid rgba(7,17,27,.1)
							border-radius: 1px
							color: rgb(147, 153, 159)
							background: #fff
					.time
						position: absolute
						top: 0
						right: 0
						line-height: 12px
						font-size: 10px
						color: rgb(147,153,159)
						
						
									
</style>