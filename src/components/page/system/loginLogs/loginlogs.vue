<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-cascades"></i> 登录日志</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="el-icon-delete" class="handle-del mr10">批量删除</el-button>
                <el-input placeholder="筛选关键词" class="handle-input mr10"></el-input>
                <el-button type="primary" icon="el-icon-search" >搜索</el-button>
            </div>
            <el-table  :data="tableData"  border class="table" ref="multipleTable"  @selection-change="handleSelectionChange">
                <el-table-column type="index" label="序号" width="55" align="center" ></el-table-column>
                <el-table-column prop="userName" label="用户名"  align="center" ></el-table-column>
                <el-table-column prop="loginName" label="登录名"  align="center" ></el-table-column>
                <el-table-column prop="roleName" label="角色名"  align="center" ></el-table-column>
                <el-table-column prop="createTime" label="登录时间" align="center"  ></el-table-column>
                <el-table-column prop="ip" label="IP" align="center" ></el-table-column>
               
            </el-table>
          
            <div class="pagination">
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="page"
                    :page-sizes="[10, 15, 30, 50, 100]"
                    :page-size="pageSize"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="total" >
                 </el-pagination>
            </div>
        </div>

        
    </div>
</template>
  
<script>
    import { fetchData } from '../../../../api/index';
    import baseURL_ from '@/utils/baseUrl.js';
    export default {
        name: 'basetable',
        data() {
            return {
                page:1,
                total:1000,
                pageSize:10,
                tableData: [],
                multipleSelection: [],
            }
        },
        created() {
            this.getData();
        },
        methods: {
            //改变每页页数
            handleSizeChange(val){
                   this.pageSize=val;
                   this.getData();
            },
            //分页
            handleCurrentChange(val){
                   this.page=val;
                   this.getData();
            },
             handleSelectionChange(val) {
                this.multipleSelection = val;
            },
            // 初始化数据
            async getData() {
                const loginLogs = await this.$http.get(baseURL_.sysUrl+'/sysLoginLogs/list',{ 
                    params: {'page':this.page,'pageSize':this.pageSize}
                    });
                if(loginLogs.data.statusCode==200){
                  this.tableData=loginLogs.data.data.list;
                  this.total=loginLogs.data.data.total;
                  this.page=loginLogs.data.data.page;
                }
            },
            
        }
    }

</script>

<style scoped>
    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }
    .del-dialog-cnt{
        font-size: 12px;
        text-align: center
    }
    .table{
        width: 100%;
        font-size: 12px;
    }
  
    .red{
        color: #ff0000;
    }
    .mr10{
        margin-right: 10px;
    }
    .container{
        min-height:500px;
    }
</style>