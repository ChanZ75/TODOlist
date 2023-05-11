<template>
  <div class="title">TODOlist</div>
  <div class="input">
    <el-form :model="form" label-width="120px" onsubmit="return false;">
      <el-form-item label="待办事项">
        <el-input v-model="input" placeholder="请输入待办事项" class="inputItem" @keydown.enter="addItem()" />
        <el-button type="primary" @click="addItem">新增</el-button>
      </el-form-item>
      <el-form-item>
        <div>
          <el-button type="success" @click="allDone" class="button">全部完成</el-button>
          <el-button type="danger" @click="cleanDone" class="button">批量清理</el-button>
        </div>
      </el-form-item>
      <el-form-item></el-form-item>
    </el-form>
  </div>
  <el-scrollbar max-height="500px">
    <div class="todoItem" v-for="item in itemList">
      <el-checkbox v-model="item.done" :label="''" @change="handleCheck(item)" class="checkbox" />
      <el-input
        v-model="item.label"
        placeholder="请输入"
        :class="{ 'inputItem--done': item.done && item.label }"
        style="width: 500px"
        @keydown.enter="checkItem(item)"
        @blur="checkItem(item)"
      />
      <el-button type="danger" @click="delItem(item)">删除</el-button>
    </div>
  </el-scrollbar>
  <div class="undone">
    待完成数量：
    <span class="number">{{ itemList.filter((item) => !item.done).length }}</span>
  </div>
</template>
<script setup>
import { ref } from 'vue'
const input = ref('')
const itemList = ref([])

//添加待办事项
function addItem() {
  if (input.value) {
    const item = {
      label: input.value,
      done: false
    }
    //置于第一个已完成的事项之前
    itemList.value.unshift(item)
    input.value = ''
  }
  saveData()
}
//删除待办事项
function delItem(item) {
  const index = itemList.value.indexOf(item)
  itemList.value.splice(index, 1)
  saveData()
}

//已完成的事项置底
function handleCheck(item) {
  if (item.done == true) {
    const index = itemList.value.indexOf(item)
    itemList.value.splice(index, 1)
    itemList.value.push(item)
  }
  //未完成的事项置顶
  else {
    const index = itemList.value.indexOf(item)
    itemList.value.splice(index, 1)
    itemList.value.unshift(item)
  }
  saveData()
}
//待办事项为空时删除
function checkItem(item) {
  if (item.label == '') {
    delItem(item)
  }
  saveData()
}
//全部完成
function allDone() {
  itemList.value.forEach((item) => {
    item.done = true
  })
  saveData()
}
//批量清理
function cleanDone() {
  itemList.value = itemList.value.filter((item) => !item.done)
  saveData()
}
//将数据保存在localStorage中
function saveData() {
  const data = JSON.stringify(itemList.value)
  localStorage.setItem('itemList', data)
}

//页面加载时读取localStorage中的数据
function loadData() {
  const data = localStorage.getItem('itemList')
  if (data !== null) {
    itemList.value = JSON.parse(data)
  }
}
loadData()
</script>

<style lang="scss">
.title {
  font-size: 30px;
  font-weight: bold;
  margin-bottom: 20px;
  text-align: center;
  margin-top: 5%;
}
.input {
  display: flex;
  justify-content: center;
  align-items: center;
  .inputItem {
    width: 500px;
    margin-right: 20px;
    justify-content: center;
  }
  .button {
    margin-left: 100px;
  }
  .el-input {
    border-radius: 20px;
  }
  .el-input__wrapper {
    border-radius: 20px;
  }
}

.todoItem {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 10px;
  .el-input__wrapper {
    display: inline-flex;
    flex-grow: 1;
    align-items: center;
    justify-content: center;
    padding: 1px 11px;
    background-color: var(--el-input-bg-color, var(--el-fill-color-blank));
    background-image: none;
    border-radius: var(--el-input-border-radius, var(--el-border-radius-base));
    transition: var(--el-transition-box-shadow);
    transform: translate3d(0, 0, 0);
    box-shadow: none;
  }
  .inputItem--done {
    text-decoration: line-through;
  }
}
.undone {
  display: flex;
  align-items: center;
  justify-content: center;
  .number {
    font-size: 26px;
    font-weight: bold;
    color: rgb(46, 117, 199);
    margin: 0 10px;
  }
}
</style>
