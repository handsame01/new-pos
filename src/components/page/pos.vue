<template>
  <div class="pos">
    <div>
      <el-row>
        <el-col :span="7" id="order-list">
          <el-tabs>
            <el-tab-pane label="点餐">
              <el-table :data="tableData" border show-summary style="width: 100%">
                <el-table-column prop="goodsName" label="商品名称" ></el-table-column>
                <el-table-column prop="count" label="量" width="55"></el-table-column>
                <el-table-column prop="price" label="金额" width="75"></el-table-column>
                <el-table-column label="操作" width="105" fixed="right">
                  <template slot-scope="scope">
                    <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div class="totaldiv">
                <small>数量：</small>
                {{totalCount}}
                <small>金额：</small>
                {{totalMoney}}
              </div>
              <div>
                <el-button type="warning">挂单</el-button>
                <el-button type="danger" @click="delAllGoods()">删除</el-button>
                <el-button type="success" @click="checkout()">结账</el-button>
              </div>
            </el-tab-pane>
            <el-tab-pane label="挂单">挂单</el-tab-pane>
            <el-tab-pane label="外卖">外卖</el-tab-pane>
          </el-tabs>
        </el-col>
        <!--分割线-->
        <el-col :span="17">
          <div class="often-goods">
            <div class="title">常用商品</div>
            <div class="often-goods-list">
              <ul>
                <li v-for="goods in oftenGoods" :key="goods.num" @click="addOrderList(goods)">
                  <span>{{goods.goodsName}}</span>
                  <span class="o-price">{{goods.price}}</span>
                </li>
              </ul>
            </div>
          </div>

          <div class="goods-type">
            <el-tabs>
              <el-tab-pane label="汉堡">
                <ul class="cookList">
                  <li v-for="goods in type0Goods" :key="goods.num" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%">
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="小食">
                <ul class="cookList">
                  <li v-for="goods in type1Goods" :key="goods.num" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img src="goods.goodsImg" width="100%">
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="饮料">
                <ul class="cookList">
                  <li v-for="goods in type2Goods" :key="goods.num" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img src="goods.goodsImg" width="100%">
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="套餐">
                <ul class="cookList">
                  <li v-for="goods in type3Goods" :key="goods.num" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img src="goods.goodsImg" width="100%">
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
            </el-tabs>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "Pos",
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalCount: 0,
      totalMoney: 0
    };
  },
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + "px";
  },
  created: function() {
    axios
      .get(
        "https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods"
      )
      .then(response => {
        //  console.log(response);
        this.oftenGoods = response.data;
      })
      .catch(error => {
        //  console.log(error);
        alert("网络错误");
      });
    axios
      .get(
        "https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods"
      )
      .then(response => {
        // console.log(response);
        // this.oftenGoods=response.data;
        this.type0Goods = response.data[0];
        this.type1Goods = response.data[1];
        this.type2Goods = response.data[2];
        this.type3Goods = response.data[3];
      })
      .catch(error => {
        //   console.log(error);
        alert("网络错误，不能访问");
      });
  },
  methods: {
    addOrderList(goods) {
      //判断商品是否存在
      this.totalCount = 0; //汇总数量清0
      this.totalMoney = 0;
      let isHave = false;
      //判断是否这个商品已经存在于订单列表
      for (let i = 0; i < this.tableData.length; i++) {
        // console.log(this.tableData[i].goodsId);
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true; //存在
        }
      }
      if (isHave) {
        //存在就进行数量添加
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        arr[0].count++;
        //console.log(arr);
      } else {
        //不存在就推入数组
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
      this.getAllMoney();
    },
    //删除单个商品
    delSingleGoods(goods) {
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      this.getAllMoney();
    },
    //删除所有商品
    delAllGoods(){
    this.tableData = [];
    this.totalCount=0;
    this.totalMoney=0;
    },
    //进行数量和价格的汇总计算
    getAllMoney(){
    this.totalCount=0;
    this.totalMoney=0;
    if(this.tableData){
            this.tableData.forEach((element) => {
        this.totalCount+=element.count;
        this.totalMoney=this.totalMoney+(element.price*element.count);   
    });
    }
  },
  checkout(){
      if(this.tableData!=0){
          this.delAllGoods();
          alert("谢谢，祝您生活愉快");
      }else{
          alert("很抱歉，您还没有点任何东西");
      }
  }
  }
  }
</script>
<style scoped>
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
}
.o-price {
  color: #58b7ff;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
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
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.goods-type {
  clear: both;
}
.totaldiv {
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #fff;
}
</style>