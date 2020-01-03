<template>
  <div>
    <!-- 按钮 -->
      <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
      <el-button type="danger" size="small">批量删除</el-button>
      <!-- /按钮 -->
<!-- 表格 -->
    <el-table :data="categorys">
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="name" label="栏目名称"></el-table-column>
        <el-table-column prop="num" label="序号"></el-table-column>
        <el-table-column prop="icon" label="父栏目"></el-table-column>
        <el-table-column fixed="right" label="操作">
          <template v-slot="slot">
            <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
            <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>

          </template>
        </el-table-column>
    </el-table>
    <!-- /表格 -->
    <!-- 分页 -->
    <el-pagination layout="prev, pager, next" :total="50">
        </el-pagination>
    <!-- /分页 -->
    <!-- 模态框 -->
     <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%" >
  测试:{{form}}
  <el-form :model="form" label-width="80px">
    <el-form-item label="栏目名称">
      <el-input v-model="form.categoryname"></el-input>
    </el-form-item>
     <el-form-item label="序号">
      <el-input v-model="form.num"></el-input>
    </el-form-item>
  </el-form>
 
  <span slot="footer" class="dialog-footer">
    <el-button @click="closeModalHandler" size="small">取 消</el-button>
    <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
  </span>
</el-dialog>
    <!-- /模态框 -->
  </div>
</template>
<script>
import request from '@/utils/request'  //自定义库
import querystring from 'querystring' //系统库
export default {
    methods:{
      submitHandler(){
        let url="http://localhost:6677/category/saveOrUpdate";
        //前端向后端发送请求，完成数据的保存操作
        request({
          url,
          method:"post",
          //告诉给后台我的请求体中放的是查询字符串
          headers:{
             "Content-Type":"application/x-www-form-urlencoded"
          },
          //请求体中的数据，将this.form转换为查询字符串发送给后台
          data:querystring.stringify(this.form)
        }).then((response)=>{
          this.closeModalHandler();
          this.loadData();
          this.$message({type:"success",message:response.message})
        })
      },
      //重载员工数据，需要调用
     loadData(){
       //this -> vue实例，通过vue实例访问该实例中的数据，methods
       //this.title/this.toAddHandler
       let url = "http://localhost:6677/category/findAll";
       request.get(url).then((request)=>{
         //箭头函数中的this指向外部函数中的this
         this.categorys = request.data;
       })
     },
      closeModalHandler(){
        this.visible=false;
      },
      toAddHandler(){
        this.title="录入员工信息",
        this.visible=true;
      },
      toDeleteHandler(id){
         //确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
      },
      toUpdateHandler(row){
       this.title="修改栏目信息",
       this.visible=true;
      }

    },
    data(){
      return{
        title:"录入栏目信息",
        visible:false,
        employees:[],
        //绑定数据
        form:{
          type:"category"
        }
      }
    },
     created(){
      //在页面加载出来的时候加载数据
      this.loadData();
    }
}
</script>
<style scoped>

</style>