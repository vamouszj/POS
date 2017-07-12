<template>
	<div class="pos">
		<el-row>
			<el-col :span='7' class="pos-order" id="orderList">
				<el-tabs>
					<el-tab-pane label="点餐">
						<el-table :data="tableData" border style="width: 100%">
						    <el-table-column prop="goodsName" label="商品"  ></el-table-column>
						    <el-table-column prop="count" label="量" width="70"></el-table-column>
						    <el-table-column prop="price" label="金额" width="70"></el-table-column>
						    <el-table-column  label="操作"  fixed="right">
						        <template scope="scope">
						            <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
						            <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
						        </template>
						    </el-table-column>
						</el-table>
						<div class="total-div">
							<small>数量:</small> {{totalCount}}  &nbsp; <small>金额:</small> {{totalMoney}}元
						</div>
						<div class="div-btn">
							<el-button type="warning">挂单</el-button>
							<el-button type="danger" @click="delAllGoods">删除</el-button>
							<el-button type="success" @click="checkOut">结账</el-button>
						</div>
					</el-tab-pane>
					<el-tab-pane label="挂单">
						
					</el-tab-pane>
					<el-tab-pane label="外卖"></el-tab-pane>
				</el-tabs>
			</el-col>
			<el-col :span="17">
				<div class="often-goods">
					<div class="title">常用商品</div>
					<div class="often-goods-list" >
						<ul>
							<li v-for="data in ofentGoods" @click="addOrderList(data)">
								<span>{{ data.goodsName }}</span>
								<span class="o-price">￥{{ data.price }}元</span>
							</li>
						</ul>
					</div>
				</div>

				<div class="goods-type">
					<el-tabs>
						<el-tab-pane v-for="(type, item) in typesGoods" :key="item"  :label="listNames[item]" >
							<div>
								<ul class="cookList">
									<li v-for="goods in type" @click="addOrderList(goods)">
										<span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
										<span class="foodName">{{ goods.goodsName }}</span><br>
										<span class="foodPrice">￥{{ goods.price }}元</span>
									</li>
								</ul>
							</div>
						</el-tab-pane>
					</el-tabs>
				</div>
			</el-col>
		</el-row>
	</div>
	
</template>

<script>
	import axios from 'axios';
	export default {
		name : 'Pos',
		data() {
			return {
				tableData : [],
		        ofentGoods : [],
		        typesGoods: {
		        	one : [],
		        	two : [],
		        	three : [],
		        	four : []
		       	},
		       	listNames : {
		       		one : "汉堡",
		       		two : "小食",
		       		three : "饮料",
		       		four : "套餐"
		       	},
		       	totalMoney: 0,
		       	totalCount : 0
			} //return end
		},
		created() {
			axios.get('http://jspang.com/DemoApi/oftenGoods.php')
			.then(response => {
				this.ofentGoods = response.data;
			})
			.catch(error => {
				alert("网络错误，不可访问");
			});

			axios.get('http://jspang.com/DemoApi/typeGoods.php')
			.then(response => {
				this.typesGoods.one = response.data[0];
				this.typesGoods.two = response.data[1];
				this.typesGoods.three = response.data[2];
				this.typesGoods.four = response.data[3];
			})
			.catch(error => {
				alert('网络错误，不可访问');
			});

		},
		mounted() {   //虚拟DOM加载完毕之后
			var orderHeight = document.body.clientHeight;
			document.getElementById("orderList").style.height = orderHeight + 'px';
		},
		methods : {
			addOrderList(goods) {
				//查看商品是否存在于订单列表中

				let isHave = false;
				for(let i = 0, len = this.tableData.length; i < len; i++) {
						if(this.tableData[i].goodsId == goods.goodsId) {
							isHave = true;
						}
				}
				//根据判断的值编写业务逻辑
				if(isHave) {
					let arr = this.tableData.filter(o =>o.goodsId == goods.goodsId);
					arr[0].count++;
				}else {
					let newGoods = {goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
					this.tableData.push(newGoods);
				}

				//汇总的价格和数量
				this.computedCountAndMoney();
			},
			// 删除单个商品
			delSingleGoods(goods) {
				this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
				this.computedCountAndMoney();
			},
			delAllGoods() {
				this.tableData = [];
				this.totalCount = 0;
				this.totalMoney = 0;
			},
			//计算汇总的价格和数量
			computedCountAndMoney() {
				this.totalCount = 0;
				this.totalMoney = 0;
				if(this.tableData) {
					this.tableData.forEach((element) => {
						this.totalCount += element.count;
						this.totalMoney += element.price * element.count;
					});					
				}
			},
			checkOut() {
				if(this.totalCount) {
					this.tableData = [];
					this.totalCount = 0;
					this.totalMoney = 0;
					this.$message({
						message: '结账成功，欢迎下次光临',
						type: "success"
					});				
				}else {
					this.$message.error("订单为空，不可支付");
				}
			}
		}//methods end
	}	
</script>

<style scoped>
	.pos-order {
		text-align: center;
		background-color: #F9FAFC;
		border-right: 1px solid #C0CCDA;
	}
	.div-btn {
		margin-top: 10px;
	}
	.title {
		height: 20px;
		border-bottom: 1px solid #D3DCE6;
		border-color: #F9FAFC;
		padding: 10px;
		text-align: left;
		background-color: #F9FAFC;
	}
	.often-goods-list ul li {
		list-style: none;
		float: left;
		background-color: #FFF; 
		border: 1px solid #E5E9F2;
		padding: 10px;
		margin: 10px;
		cursor: pointer;
	}
	.o-price {
		color: #58B7FF;
	}
	.goods-type {
		clear: both;
	}
	.cookList li {
		list-style: none;
		width: 23%;
		border: 1px solid #E5E9F2;
		height: auto;
		overflow: hidden;
		background-color: #FFF;
		padding: 1px;
		float: left;
		margin: 2px;
		cursor: pointer;
	}
	.cookList li span {
		display: block;
		float: left;
	}
	.foodImg {
		width: 40%;
	}
	.foodName {
		font-size: 18px;
		padding-left: 10px;
		color: brown;
	}
	.foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .total-div {
   	  background-color: #fff;
   	  padding: 10px;
   	  border-bottom: 1px solid #D3DCE6;
   }
</style>