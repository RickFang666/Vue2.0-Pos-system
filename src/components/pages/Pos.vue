<template>
  <div class="pos">
    <el-row>
      <el-col :span='7' class='pos-order' id='order-list'>
      <div>我是订单栏</div>
        <el-tabs>
          <el-tab-pane label='点餐'>
            <el-table :data="tableData" border  style="width: 100%" >
              <el-table-column prop="goodsName" label="商品"  ></el-table-column>
              <el-table-column prop="count" label="量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <el-table-column  label="操作" width="100" fixed="right">
                <template scope="scope" >
                    <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="total"><span>数量:{{totalCount}}</span>
            <span>总价:{{totalPrice}}元</span>
            </div>

            <div class='div-btn'>
              <el-button type='warning' @click="holdOrder()">挂单</el-button>
              <el-button type='danger' @click="delAllGoods()">删除</el-button>
              <el-button type='success' @click="checkout()">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label='挂单'>挂</el-tab-pane>
          <el-tab-pane label='外卖'>外</el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col :span='17'>
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for='goods in oftenGoods' @click=addOrderList(goods)>
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label='汉堡'>
              <div>
                <ul class='cookList'>
                  <li v-for='goods in type0Goods' @click=addOrderList(goods)>
                      <span class="foodImg"><img :src='goods.goodsImg' width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label='小食'>
              <div>
                <ul class='cookList'>
                  <li v-for='goods in type1Goods' @click=addOrderList(goods)>
                      <span class="foodImg"><img :src='goods.goodsImg' width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label='饮料'>
              <div>
                <ul class='cookList'>
                  <li v-for='goods in type2Goods' @click=addOrderList(goods)>
                      <span class="foodImg"><img :src='goods.goodsImg' width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label='套餐'>
              <div>
                <ul class='cookList'>
                  <li v-for='goods in type3Goods' @click=addOrderList(goods)>
                      <span class="foodImg"><img :src='goods.goodsImg' width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
          </el-tabs></div>
      </el-col>
    </el-row>
  </div>
</template>
<script type="text/ecmascript-6">
import axios from 'axios';
export default {
    name: 'Pos',
    data(){
      return {
          tableData: [],
          oftenGoods:[],
          type0Goods:[],
          type1Goods:[],
          type2Goods:[],
          type3Goods:[],
          totalCount:0,
          totalPrice:0,
      }
    },
    created: function (){
      axios.get('http://jspang.com/DemoApi/oftenGoods.php')
      .then(response=>{
        // console.log(response)
        this.oftenGoods=response.data;
      })
      .catch(error=>{
        // console.log(error);
        alert('network error');
      })
      axios.get('http://jspang.com/DemoApi/typeGoods.php')
      .then(response=>{
        // console.log(response)
        this.type0Goods=response.data[0];
        this.type1Goods=response.data[1];
        this.type2Goods=response.data[2];
        this.type3Goods=response.data[3];
      })
      .catch(error=>{
        // console.log(error);
        alert('network error');
      })
    }
    ,
    mounted: function() {
        var orderHeight = document.body.clientHeight;
        document.getElementById("order-list").style.height = orderHeight + 'px';
    },
    methods: {
      addOrderList(goods){
        //if in list?
        this.totalCount = 0;
        this.totalPrice = 0;
        let isHave = false;
        for (let i=0;i<this.tableData.length;i++)
        {
          if(this.tableData[i].goodsId==goods.goodsId)
            {
              isHave = true;
            }
        }
        if(isHave) {
          //change goods count
          let arr =this.tableData.filter(o=>o.goodsId == goods.goodsId);
          arr[0].count++;
        }else {
          let newGoods = {
            goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1
          }
          this.tableData.push(newGoods);
        }
        this.getTotal();

      },
      delSingleGoods(goods){
        this.tableData = this.tableData.filter(o=>o.goodsId!=goods.goodsId);
        this.getTotal();
      },
      getTotal(){
        this.totalCount=0;
        this.totalPrice=0;
        if(this.tableData){
            this.tableData.forEach((element) => {
            this.totalCount+=element.count;
            this.totalPrice=this.totalPrice+(element.price*element.count);
        });
      }
    },
      delAllGoods(){
        this.tableData=[];
        this.totalCount=0;
        this.totalPrice=0;
      },
      checkout() {
    if (this.totalCount!=0) {
        this.tableData = [];
        this.totalCount = 0;
        this.totalPrice = 0;
        this.$message({
            message: '结账成功，请继续下一单点餐!',
            type: 'success'
        });

      }else{
          this.$message.error('数量为空，请勿提交结账！');
      }

    },
    holdOrder(){
        this.$message.error('此功能暂未开放');
    }
  }
}
</script>
<style rel="stylesheet" href="" scoped>
.pos-order {
    background-color: #f9fafc;
    border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 10px;
}
 .title{
       height: 20px;
       border-bottom:1px solid #D3DCE6;
       background-color: #F9FAFC;
       padding:10px;
       text-align: left;
   }
   .often-goods-list ul li{
      list-style: none;
      float:left;
      border:1px solid #E5E9F2;
      padding:10px;
      margin:5px;
      background-color:#fff;
      cursor: pointer;
   }
  .o-price{
      color:#58B7FF;
   }
.goods-type{
  clear: both;
}
.cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       cursor: pointer;

   }
   .cookList li span{

        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 16px;
       padding-left: 10px;
       color:brown;

   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .total {
       background-color:#f9fafc;
       border-bottom: 1px solid #d1dbe5;
   }
   .total>span {
    display: inline-block;
    width: 49%;
   }
</style>
