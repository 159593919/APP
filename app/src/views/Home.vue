<template>

 <el-container>
  <el-header>Header</el-header>
  <el-container> 
    <el-aside width="200px">Aside</el-aside>
<el-main>
<el-button type="primary" @click="dialogFormVisible=true">添加</el-button>
  <el-table :data="tableData"  style="width: 100%"><el-table-column   label="姓名" prop="name"> </el-table-column>
   <el-table-column label="年龄" prop="age"></el-table-column>
          <el-table-column align="right">
  <template slot="header"> 操作 </template>
<template slot-scope="scope">
<el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
  <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
  </template>                          
   </el-table-column>                         
  </el-table>                          
  <el-pagination background layout="sizes,prev, pager, next" :total="total" :page-size="limit"
   @current-change="changePage"  @size-change="handleSizeChange" :page-sizes="[1, 2, 4, 6]"   >  
   </el-pagination>                
       </el-main>              
    <el-dialog title="添加" :visible.sync="dialogFormVisible"></el-dialog>               
 </el-container>
</el-container>
  </template>

 <script>
 import axios from 'axios'
export default {
data(){
  return{
tableData:[],
pagenum:1,
limit:4,
total:0,
dialogFormVisible:false,
form:{
name:"",
age:""
},
formLabelWidth: '120px',
id:null
 
  }
},
created() {
  this.getdata()
},
methods: {
 getdata(){
   axios.get("/api/list",{params:{pagenum:this.pagenum,limit:this.limit}}).then(res=>{
    if(res.data.code===1){
      this.tableData=res.data.data
      this.total=res.data.total;


    }else{
      alert(res.data.msg)
    }
   })
 },
 handleEdit(index, row) {
  this.dialogFormVisible=true;
  this.id=row.id;
  this.form={
    name:row.name,
    age:row.age
  }
      },
     handleDelete(index, row) {
           this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                //删除接口
                axios.get('/api/del',{params:{id:row.id}}).then(res => {
                    if(res.data.code === 1){
                        this.$message({
                            type: 'success',
                            message: '删除成功!'
                        });
                       
                    }else{
                        this.$message({
                            type: 'info',
                            message: res.data.msg
                        });
                    }
                })
            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '已取消删除'
                });          
            });
      },
      changePage(cur){
        this.pagenum = cur;
        this.getdata()
      },
      curFun(){
        let url='';
        if(this.id){
          url='/api/edit'
        }else{
          url="/api/add"
        }
        axios.post(url,{...this.form,id:this.id}).then(res=>{
          if(res.data.code===1){
            this.getdata()
          }
          this.reset()
           this.$message({
                    type: 'info',
                    message:res.data.msg
                }); 
                this.dialogFormVisible = false;
          
        })
      },
       reset(){
        this.form = {
            name: "",
            age:""
           
            
            
        }
        this.id = null; 
      },
     
      handleSizeChange(num){
          this.limit = num;
          this.getdata();
      }
      }

}
</script>
<style >
  
  .el-header, .el-footer {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }
  
  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 200px;
  }
  
  /* .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 160px;
  } */
  
.el-container {
    height: 100%;
    overflow: hidden;
  }
  
  
</style>
