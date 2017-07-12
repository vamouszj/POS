<template>
	<div class="left-nav">
		<ul>
			<li>
				<i class="icon iconfont icon-goumai"></i>
				<div>收银</div>
			</li>
			<li>
				<i class="icon iconfont icon-dianpu"></i>
				<div>店铺</div>
			</li>
			<li>
				<i class="icon iconfont icon-hanbao"></i>
				<div>商品</div>
			</li>
			<li>
				<i class="icon iconfont icon-huiyuanqia"></i>
				<div>会员</div>
			</li>
			<li>
				<i class="icon iconfont icon-31guanzhuxuanzhong"></i>
				<div>统计</div>
			</li>												

		</ul>
	</div>
</template>

<script>
	export default {
		name : 'leftNav'
	}
</script>

<style scoped>
	.left-nav {
		color: #fff;
		font-size: 10px;
		height: 100%;
		background-color: #1D8CE0;
		float: left;
		width: 5%;
	}
	.iconfont {
		font-size: 24px;

	}
	.left-nav ul {
		padding: 0;
		margin: 0;
	}
	.left-nav li {
		list-style: none;
		text-align: center;
		border-bottom: 1px solid #20A0FF;
		padding: 10px;
	}
</style>