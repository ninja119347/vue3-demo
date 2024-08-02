
<template>
  <div class="table-box">
    <div class="title">
      <h2>最简单的 CRUD Demo</h2>
    </div>
    <!-- query -->
    <div class="query-box">
      <el-input class="query-input" v-model="queryInput"  placeholder="Please search by name" @change="handleQueryName" />
      <div class="btn-list">
        <el-button type="primary" @click="handleAdd" >Add</el-button>
      <el-button type="danger" @click="handleDelList"   v-if="multipleSelection.length>0"  >Mul-delete</el-button>
      </div>
      
    </div>
    <!-- table -->  
    <el-table
    border
    ref="multipleTableRef"
    :data="tableData"
    style="width: 100%"
    @selection-change="handleSelectionChange">
    <el-table-column type="selection" width="55" />
    <el-table-column prop="id" label="id" width="120"/>
    <el-table-column prop="postId" label="post_id" width="120"/>
    <el-table-column prop="deptId" label="dept_id" width="120"/>
    <el-table-column prop="username" label="Name" width="120" />
    <el-table-column prop="nickname" label="nickname" width="120"/>
    <el-table-column prop="email" label="Email" width="200" />
    <el-table-column prop="status" label="State" width="120" />
    <el-table-column prop="phone" label="Phone" width="120" />
    <el-table-column prop="address" label="Address" width="300" />
    <el-table-column prop="createTime" label="createTime" width="200" />
    <el-table-column fixed="right" label="Operations" min-width="120">
      <template #default="scope">
        <el-button link
          type="primary"
          size="small"
          @click="handleRowDel(scope.row)" 
          style="color: #F56C6C;">
          Delete
        </el-button>
        <el-button link type="primary" size="small" @click="handleEdit(scope.row)">Edit</el-button>
      </template>
    </el-table-column>
    </el-table>
    <el-pagination
     background
     layout="prev, pager, next" 
     :total="total" 
     v-model:current-page="curPage"
     :page-size="pageSize"
     @current-change="handleChangePage"
     @size-change="handleUpdatePageSize" 
     style="display:flex; justify-content:center; margin-top:10px" 
     />
    <!------------------- dialog --------------------->
    <el-dialog v-model="dialogFormVisible" :title = "dialogType==='add'? 'Add':'Edit'" width="500">
    <el-form :model="tableForm">
      <el-form-item label="姓名" :label-width="80">
        <el-input v-model="tableForm.username" autocomplete="off" />
      </el-form-item>
      <el-form-item label="密码" :label-width="80">
        <el-input v-model="tableForm.password" autocomplete="off" />
      </el-form-item>
      <el-form-item label="邮箱" :label-width="80">
        <el-input v-model="tableForm.email" autocomplete="off" />
      </el-form-item>
      <el-form-item label="电话" :label-width="80">
        <el-input v-model="tableForm.phone" autocomplete="off" />
      </el-form-item>
      <el-form-item label="状态" :label-width="80">
        <el-input v-model="tableForm.state" placeholder="Please select a state"/>
        </el-form-item>
      <el-form-item label="地址" :label-width="80">
        <el-input v-model="tableForm.address" autocomplete="off" />
      </el-form-item>
    </el-form>
    <template #footer>
      <div class="dialog-footer">
        <el-button @click="dialogFormVisible = false">Cancel</el-button>
        <el-button type="primary" @click="dialogconfirm">
          Confirm
        </el-button>
      </div>
    </template>
  </el-dialog>
  </div>
</template>

<script setup>
import { ta } from 'element-plus/es/locales.mjs';
import { ref } from 'vue'
import request from './utils/request.js'
import { tabsEmits } from 'element-plus';

// data
let queryInput = ref('')
let tableData = ref()
let multipleSelection = ref([])
let dialogFormVisible = ref(false)
let tableForm = ref({

},)
let dialogType = ref('add')
let tableDataCopy = []
let total = ref(50)
let curPage = ref(1)
let pageSize = ref(10)
  // methods
  const handleUpdatePageSize = (newSize) => {
  pageSize.value = newSize
  console.log(newSize)
  getTableData()
}
const handleChangePage =(val)=>{
  getTableData(val)
}
const handleChangeSize =(val)=>{
  pageSize.value = val
  console.log(val)
  getTableData()
}
  // 格式化日期函数
function formatDate(date) {
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  const day = String(date.getDate()).padStart(2, '0')
  const hours = String(date.getHours()).padStart(2, '0')
  const minutes = String(date.getMinutes()).padStart(2, '0')
  const seconds = String(date.getSeconds()).padStart(2, '0')
  return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`
}
const getTableData = async (cur = 1) => {
  // let res = await request.get('api/user/list',  {pageSize: 5, pageNum:cur})
  let res = await request.get(`api/user/list?page=${cur}&pageSize=9`)
  tableData.value = res.data
  // tableDataCopy= Object.assign([],tableData.value)
  console.log(res)
}
getTableData()
//查询数据by name
let handleQueryName = async (val) => {
  // tableData.value = tableDataCopy
  // // console.log(queryInput.value)
  // if (val.length>0){
  //   tableData.value = tableData.value.filter(item => (item.username).toLowerCase().match(val.toLowerCase()))
  // }else{
  //   console.log('length=0')
  //   tableData.value = tableDataCopy
  // }
  if (val.length === 0) {
    await getTableData()
    return
  }
  let res = await request.get(`api/user/${val.toString()}`)
  tableData.value = res.data
  
}
  //选中
const handleSelectionChange = (val) => {
  multipleSelection.value = []
  val.forEach(element => {
      multipleSelection.value.push(element.id)
    })
}
// 添加
let handleAdd = () => {
  dialogFormVisible.value = true
  dialogType.value = 'add'
  tableForm.value={}
}
//
let handleRowClick = () => {
  console.log('click')
}
// 确认
let dialogconfirm =async () => {
  dialogFormVisible.value = false
  if(dialogType.value === 'add') {
  //1. 拿到数据
  //2. 添加到table中
    console.log("add data: ",tableForm.value)
    let res = await request.post('register',{
      ...tableForm.value,
      // id: (tableData.value.length + 1),
    })
    //刷新数据
    await getTableData(curPage.value)
    // tableData.value.push({id:(tableData.value.length+1).toString(),createTime:(formatDate(new Date())),...tableForm.value})
  } else {
    await request.put('api/updateUser',{
      ...tableForm.value,
    })
    await getTableData(curPage.value)
  }
}
//删除一条
const handleRowDel = async ({id}) => {
  await request.delete(`api/delete/`+id)
  await getTableData(curPage.value)
}
//删除多个选中
let handleDelList = () => {
  //1. 通过id获取条目对应的索引值
  multipleSelection.value.forEach(id => {
    handleRowDel({id})
  })
  //2. 通过索引值删除对应条目
  // tableData.value = tableData.value.filter(item => !indexs.includes(item.id))
}
//编辑
const handleEdit = (row) => {
  dialogFormVisible.value = true
  dialogType.value = "edit"
  tableForm.value = { ...row }
  }
</script>

<style scoped>
.table-box {
  width: 800px;
  margin: 200px auto;
}
.title {
  text-align: center;
  margin-bottom: 20px;
}
.query-box {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}
.el-iput {
  width: 240px;
}
.query-input {
  width: 200px;
}
</style>
<!-- /*TODO
* axios 请求后端接口
* 使用go完成增删改查的接口
* mysql数据库
*/   -->