<template>
    <div>
        <h>产品管理</h>
        <!-- 按钮 -->
        <el-button type="success" @click="toAddHandler">添加</el-button>
        <el-button type="danger" >批量删除</el-button>
        <!-- /按钮 -->
        <!-- 表格 -->
        <!-- "id": 4192,
       "name": "清洗油烟机",
       "description": "11111327777",
       "price": 11,
       "status": "9139",
       "photo": "http://134.175.154.93:8888/group1/M00/00/19/rBAACV2QnHaAGTC4AADX6uXN4zc85.jpeg",
       "categoryId": 9358 -->
        <el-table :data="products">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="名字"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="categoryId" label="所属产品" ></el-table-column>
             <el-table-column prop="status" label="状态" ></el-table-column>
            <el-table-column label="操作">
                <!-- v-slot用于获取当前行数据 -->
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>

        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
         <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- /分页 -->
        <!-- 模态框    冒号表示引用脚本-->
         <el-dialog :title="title" :visible.sync="visible" width="60%" >
         <el-form :model="form" label-width="80px">
            <el-form-item label="产品名称">
                <el-input v-model="form.name"></el-input>
            </el-form-item>
            <el-form-item label="描述">
                <el-input  v-model="form.description"></el-input>
            </el-form-item>
            <el-form-item label="价格">
                <el-input v-model="form.price"></el-input>
            </el-form-item>
        </el-form>
         <span slot="footer" class="dialog-footer">
         <el-button @click="visible = false">取 消</el-button>
         <!-- @click=onclick -->
         <el-button type="primary" @click="submitHandler">确 定</el-button>
         </span>
        </el-dialog>
        <!-- /模态框 -->

    </div>
</template>

<script>
//暴露接口
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    // 存放网页中需要调用的方法
    methods:{
        submitHandler(){
          let url="http://localhost:6677/product/saveOrUpdate";
          request({
          url,
          method:"POST",
          //为了告诉后台我的请求体中放的是查询字符串
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
          //将this.form转换为查询字符串发送给后台
          data: querystring.stringify(this.form)
          }).then((response)=>{
              //请求结束
              this.closeModalHandler;
              //提示消息
              this.loadData();
              this.$message({
                  type:"success",
                  message:response.message
              })
              
          })
        },
        loadData(){
          let that=this
          let url = "http://localhost:6677/product/findAll"
          request.get(url).then(function(response){
          //将查询结果设置到employees里
          //箭头函数中的this指向外部函数的this
          //原先this是指向vue实例，通过vue实例访问该实例中的数据，methods中
          that.products=response.data;
      })
        },
        toDeleteHandler(id){
            // 确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
        })
        
        },
        toUpdateHandler(row){
            this.title="修改产品信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
       toAddHandler(){
            this.title="添加产品信息";
           this.visible=true;
       }
    },
    //用于存放要在网页中存放的数据
    data(){
        return {
            title:"录入产品信息",
            visible:false,
            products:[],
            form:{
                type:"product"
            }
        }
    },
    created(){
        //在页面加载出来之前加载数据
        this.loadData();
    },
}
</script>

<style scoped>
       
</style>
