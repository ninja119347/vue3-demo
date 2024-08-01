
<template>
  <div class="table-box">
    <div class="title">
      <h2>最简单的 CRUD Demo</h2>
    </div>
    <!-- query -->
    <div class="query-box">
      <el-input class="query-input" v-model="queryInput"  placeholder="Please search by name" @input="handleQueryName" />
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
    <el-table-column prop="name" label="Name" width="120" />
    <el-table-column prop="email" label="Email" width="200" />
    <el-table-column prop="state" label="State" width="120" />
    <el-table-column prop="phone" label="Phone" width="120" />
    <el-table-column prop="address" label="Address" width="300" />
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
    <!------------------- dialog --------------------->
    <el-dialog v-model="dialogFormVisible" :title = "dialogType==='add'? 'Add':'Edit'" width="500">
    <el-form :model="tableForm">
      <el-form-item label="姓名" :label-width="80">
        <el-input v-model="tableForm.name" autocomplete="off" />
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
// data
let queryInput = ref('')
let tableData = ref([ {
    id:"1",
    name:"Tom1",
    email:"1193471036@qq.com",
    phone:"1193471036",
    state:"California",
    address:"广东省广州市天河区",

  },
  {
    id:"2",
    name:"Tom2",
    email:"1193471036@qq.com",
    phone:"1193471036",
    state:"California",
    address:"广东省广州市天河区",

  },
  {
    id:"3",
    name:"Tom3",
    email:"1193471036@qq.com",
    phone:"1193471036",
    state:"California",
    address:"广东省广州市天河区",

  },
  {
    id:"4",
    name:"Tom4",
    email:"1193471036@qq.com",
    phone:"1193471036",
    state:"California",
    address:"广东省广州市天河区",

  },
  {
    id:"5",
    name:"Tom5",
    email:"1193471036@qq.com",
    phone:"1193471036",
    state:"California",
    address:"广东省广州市天河区",

  },])
let multipleSelection = ref([])
let dialogFormVisible = ref(false)
let tableForm = ref({
  name: '张三',
  email: '123@qq.com ',
  phone: '1192371384',
  state: '在职',
  address: '广东省',
},)
let dialogType = ref('add')
let tableDataCopy = Object.assign([],tableData.value)
  // methods

let handleQueryName = (val) => {
  tableData.value = tableDataCopy
  // console.log(queryInput.value)
  if (val.length>0){
    tableData.value = tableData.value.filter(item => (item.name).toLowerCase().match(val.toLowerCase()))
  }else{
    console.log('length=0')
    tableData.value = tableDataCopy
  }
  
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
let dialogconfirm = () => {
  dialogFormVisible.value = false
  if(dialogType.value === 'add') {
  //1. 拿到数据
  //2. 添加到table中
    tableData.value.push({id:(tableData.value.length+1).toString(),...tableForm.value})
  } else {
    let index = tableData.value.findIndex(item => item.id === tableForm.value.id)
    tableData.value[index] = tableForm.value
  }
  tableDataCopy = Object.assign([],tableData.value)
  
}
//删除一条
let handleRowDel = ({id}) => {
  console.log(id)
  //1. 通过id获取条目对应的索引值
  let index = tableData.value.findIndex(item => item.id === id)
  //2. 通过索引值删除对应条目
  tableData.value.splice(index, 1)
  // tableData.value = tableData.value.filter(item => item.id !== row.id)
  tableDataCopy = Object.assign([],tableData.value)
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
  console.log(row)
  tableForm.value = { ...row }
  tableDataCopy = Object.assign([],tableData.value)
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