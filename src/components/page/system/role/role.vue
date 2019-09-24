<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-cascades"></i> 角色管理</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="el-icon-delete" class="handle-del mr10">批量删除</el-button>
                <el-button type="primary" icon="el-icon-delete" class="handle-del mr10" @click="add">添加</el-button>
                <el-input placeholder="筛选关键词" class="handle-input mr10"></el-input>
                <el-button type="primary" icon="el-icon-search" >搜索</el-button>
            </div>
            <el-table :data="tableData" border class="table" ref="multipleTable"  @selection-change="handleSelectionChange">
                <el-table-column type="selection" width="55" align="center" ></el-table-column>
                <el-table-column type="index" label="序号" width="55" align="center" ></el-table-column>
                <el-table-column prop="roleName" label="角色名称"  align="center" width="200"></el-table-column>
                <el-table-column prop="roleCode" label="CODE"  align="center" width="150"></el-table-column>
                <el-table-column prop="createTime" label="创建时间" align="center"  width="200"></el-table-column>
                <el-table-column prop="isAdmin" label="是否管理员" align="center" width="150"></el-table-column>
                <el-table-column prop="isBuiltIn" label="是否内置"  align="center" width="150"></el-table-column>
                <el-table-column label="操作" width="" align="center">
                    <template slot-scope="scope">
                        <el-button type="text" icon="el-icon-edit" >编辑</el-button>
                        <el-button type="text" icon="el-icon-delete" class="red" >删除</el-button>
                    </template>
                </el-table-column>
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

        <!-- 编辑弹出框 -->
        <el-dialog :title="titleName"  :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="70px">
                <el-form-item label="角色名称">
                <el-input v-model="form.roleName" class="input"></el-input>
                </el-form-item>
                <el-form-item label="CODE">
                    <el-input v-model="form.roleCode" class="input"></el-input>
                </el-form-item>
                
                <el-form-item label="权限菜单" >
                    
                     <el-tree :data="treeData"
                        show-checkbox
                        default-expand-all
                        node-key="id"
                        ref="tree"
                        highlight-current
                        :props="defaultProps"
                         style="height:200px;overflow:auto;"
                        >
                        </el-tree>

                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>
  
<script>
    import { fetchData } from '../../../../api/index';
    import baseURL_ from '@/utils/baseUrl.js';
    export default {
        name: 'basetable',
        data() {
            return {
                editVisible: false,
                page:1,
                total:1000,
                pageSize:10,
                tableData: [],
                multipleSelection: [],
                treeData:{},
                form:{

                },
                defaultProps: {
                children: 'children',
                label: 'name'
                }
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
            async add(){
               this.form={};
               this.editVisible=true;
               this.titleName="添加";
               if(this.treeData!=null){
                 const role = await this.$http.get(baseURL_.sysUrl+'/sysRole/getPermissions');
                 this.treeData=role.data.data;
               }
               

            },
            // 初始化数据
            async getData() {
                const role = await this.$http.get(baseURL_.sysUrl+'/sysRole/list',{ 
                    params: {'page':this.page,'pageSize':this.pageSize}
                    });
                if(role.data.statusCode==200){
                  this.tableData=role.data.data.list;
                  this.total=role.data.data.total;
                  this.page=role.data.data.page;
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
    .el-form-item__content{
        height:150px !important;
        background-color:red !important;
    }
</style>
