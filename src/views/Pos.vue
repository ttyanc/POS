<template>
  <div class="pos">
    <el-row>
      <el-col :span="7">
        <el-tabs>
          <el-tab-pane label="点餐">点餐</el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>
        <el-table :data="tableData" border style="width: 100%">
          <el-table-column prop="goodsName" label="商品"></el-table-column>
          <el-table-column prop="count" label="量" width="50"></el-table-column>
          <el-table-column prop="price" label="金额" width="70"></el-table-column>
          <el-table-column label="操作" width="100" fixed="right">
            <template slot-scope="scope">
              <el-button type="text" size="small" @click="deleteSingle(scope.row)">删除</el-button>
              <el-button type="text" size="small" @click="addGoodsList(scope.row)">增加</el-button>
            </template>
          </el-table-column>
        </el-table>
        <div>
          <span>数量：{{total}}</span>
          <span>金额：{{totalMoney}}</span>
        </div>
        <el-button type="warning">挂单</el-button>
        <el-button type="danger" @click="deleteAll">删除</el-button>
        <el-button type="success" @click="checkout">结账</el-button>
      </el-col>
      <el-col :span="17">
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="item in oftenGoods" :key="item.goodsId" @click="addGoodsList(item)">
                <span>{{item.goodsName}}</span>
                <span class="o-price">￥{{item.price}}元</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <ul class="cookList">
                <li v-for="item in type0Goods" :key="item.goodsId" @click="addGoodsList(item)">
                  <span class="foodImg">
                    <img :src="item.goodsImg" width="100%" />
                  </span>
                  <span class="foodName">{{item.goodsName}}</span>
                  <span class="foodPrice">￥{{item.price}}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="小食">小食</el-tab-pane>
            <el-tab-pane label="饮料">饮料</el-tab-pane>
            <el-tab-pane label="套餐">套餐</el-tab-pane>
          </el-tabs>
        </div>
      </el-col>
    </el-row>
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
      total: 0,
      totalMoney: 0
    };
  },
  created() {
    // 这两种形式都可以
    //  this.$axios.get("http://localhost:8080/api/type0Goods").then(
    this.$axios.get("/type0Goods").then(
      response => {
        this.type0Goods = response.data;
      },
      error => {
        console.log("请求失败了", error);
      }
    );
    // 这两种形式都可以
    // this.$axios.get("http://localhost:8080/api/oftenGoods").then(
    this.$axios.get("/oftenGoods").then(
      response => {
        this.oftenGoods = response.data;
      },
      error => {
        console.log("请求失败了", error);
      }
    );
  },
  methods: {
    addGoodsList(item) {
      // 定义是否已有该商品
      let isHave = false;
      let findItem = this.tableData.find(val => val.goodsId === item.goodsId);
      if (findItem) {
        isHave = true;
      }
      // 如果有，改变数量
      if (isHave) {
        let row = this.tableData.filter(o => o.goodsId === findItem.goodsId);
        row[0].count++;
      } else {
        // 如果没有，添加一个商品
        this.tableData.push({ ...item, count: 1 });
      }
      this.getAllMoney();
    },
    getAllMoney() {
      this.total = 0;
      this.totalMoney = 0;
      this.tableData &&
        this.tableData.map(item => {
          this.total += item.count;
          this.totalMoney += item.count * item.price;
        });
    },
    deleteSingle(row) {
      this.tableData = this.tableData.filter(
        item => item.goodsId != row.goodsId
      );
      this.getAllMoney();
    },
    deleteAll() {
      this.tableData = [];
      this.total = 0;
      this.totalMoney = 0;
    },
    checkout() {
      if (this.total != 0) {
        this.tableData = [];
        this.total = 0;
        this.totalMoney = 0;
        this.$message({
          message: "结账成功，感谢你又为店里出了一份力!",
          type: "success"
        });
      } else {
        this.$message.error("不能空结。老板了解你急切的心情！");
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
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
.el-col-17 {
  display: flex;
  flex-direction: column;
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
</style>
