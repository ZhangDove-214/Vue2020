<template>
    <div>
        顾客管理
        <el-button type="success" @click="toAddHandler">添加</el-button>
        <el-button type="danger" >删除</el-button>

        <el-table :data="customers">
            <!-- 多选框的实现 -->
        <!-- <el-table-column type="selection" width="55">
        </el-table-column> -->
            <!-- 多选框实现完成 -->
            
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>

        </el-table>
        <!-- 分页 -->
         <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!--模态框  -->
         <el-dialog title="录入顾客信息" :visible.sync="visible" width="60%" >
        <el-form :model="form" label-width="80px">
            <el-form-item label="用户名">
                <el-input v-model="form.username"></el-input>
            </el-form-item>
              <el-form-item label="密码">
                <el-input type="password"  v-model="form.password"></el-input>
            </el-form-item>
              <el-form-item label="真实姓名">
                <el-input   v-model="form.realname"></el-input>
            </el-form-item>
              <el-form-item label="手机号">
                <el-input   v-model="form.telephone"></el-input>
            </el-form-item>

        </el-form>
         <span slot="footer" class="dialog-footer">
         <el-button @click="visible = false">取 消</el-button>
         <!-- @click=onclick -->
         <el-button type="primary" @click="submitHandler">确 定</el-button>
         </span>
        </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
//暴露接口
export default {
    // 存放网页中需要调用的方法
    methods:{
        loadData(){
      let that=this
      let url = "http://localhost:6677/customer/findAll"
      request.get(url).then(function(response){
       //将查询结果设置到customers里
       that.customers=response.data;
       
      })
        },
        submitHandler(){
          let url="http://localhost:6677/customer/saveOrUpdate";
          request({
          url,
          method:"POST",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
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
        toDeleteHandler(id){
            // 确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口，完成删除操作
            let url = "http://localhost:6677/customer/deleteById?id="+id;
            request.get(url).then((response)=>{
              //刷新数据
              this.loadData();
              //提示结果
               this.$message({
            type: 'success',
            message:response.message
          });
          })
         
        })
        
        },
        toUpdateHandler(row){
            //模态框表单中显示当前行的信息
            this.form=row;
            this.visible=true;
            
        },
        closeModalHandler(){
            this.visible=false;
        },
       toAddHandler(){
           //将form变为初始值
           this.form={
               type:"customer"
           }
           this.visible=true;

       }
    },
    //用于存放要在网页中存放的数据
    data(){
        return {
           visible:false, 
           customers:[], 
            form:{
              type:"customer"
            }        
        }
    },
    created(){
        // Vue实例创建完毕执行的操作
    this.loadData()

    }
}
</script>

<style scoped>
       
</style>
