<template>
  <d2-container>
    <el-form :inline="true" :model="formInline" class="demo-form-inline">
      <el-form-item label="宠物种类">
        <el-select v-model="formInline.type" placeholder="活动区域" @change="changType()">
          <el-option label="狗狗" value="1"></el-option>
          <el-option label="猫猫" value="2"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="科普类目">
        <el-select v-model="formInline.catalog" placeholder="请选择">
          <el-option v-for="(item, index) in catalogList" :key="item.id" :label="item.catalogName" :value="index">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">查询</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="tableData" border style="width: 100%" height="550">
      <el-table-column prop="smallName" label="文章封面图片" width="180" align="center">
        <template slot-scope="scope">
          <img :src="scope.row.image"  min-width="70" height="70" style="border-radius:5px;"/>
        </template>
      </el-table-column>
      <el-table-column prop="smallName" label="文章标题" width="180" align="center">
      </el-table-column>
      <el-table-column prop="readNum" label="阅读量" width="180" align="center">
      </el-table-column>
      <el-table-column prop="likeNum" label="点赞量" align="center">
      </el-table-column>
      <el-table-column label="操作" align="center">
      <template slot-scope="scope">
        <el-button
          size="mini"
          @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
        <el-button
          size="mini"
          type="danger"
          @click="handleDelete(scope.$index, scope.row)">删除</el-button>
      </template>
    </el-table-column>
    </el-table>
  </d2-container>
</template>

<script>
import axios from 'axios'
const baseUrl = 'http://localhost:8093/lpCms/wiki'
export default {
  name: 'page-home',
  data () {
    return {
      formInline: {
        type: '1',
        catalog: ''
      },
      catalogList: [],
      tableData: [],
      test: ''
    }
  },
  mounted () {
    console.log('初始化')
    console.log(this.catalogList)
    console.log(this.tableData)
    axios.get(baseUrl + '/catalogs?type=' + this.formInline.type)
      .then(response => {
        this.catalogList = response.data.data
        console.log(this.catalogList)
      }, err => {
        console.log(err)
      }).catch((error) => {
        console.log(error)
      })
  },
  methods: {
    onSubmit () {
      var catalog = this.formInline.catalog
      console.log(catalog)
      this.tableData = this.catalogList[catalog].catalogLists
      console.log(this.tableData)
    },
    changType () {
      console.log(this.formInline.type)
      axios.get(baseUrl + '/catalogs?type=' + this.formInline.type)
        .then(response => {
          this.catalogList = response.data.data
          console.log(this.catalogList)
        }, err => {
          console.log(err)
        }).catch((error) => {
          console.log(error)
        })
    },
    handleEdit (index, row) {
      console.log(index, row)
      this.$router.push({ name: 'page-demo', params: { articleId: row.id } })
    },
    handleDelete (index, row) {
      this.$message.warning('啥？我好不容易爬出来的数据，不许删！')
    }
  }
}
</script>

<style lang="scss" scoped>
.page {
  .logo {
    width: 120px;
  }
  .btn-group {
    color: $color-text-placehoder;
    font-size: 12px;
    margin-top: 0px;
    margin-bottom: 20px;
    .btn-group__btn {
      color: $color-text-sub;
      &:hover {
        color: $color-text-main;
      }
      &.btn-group__btn--link {
        color: $color-primary;
      }
    }
  }
}
</style>
