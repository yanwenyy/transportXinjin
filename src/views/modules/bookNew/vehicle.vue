<template>
  <div class="mod-user">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item label="进厂时间:">
        <el-date-picker
          v-model="dataForm.startTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="选择日期">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="出厂时间:">
        <el-date-picker
          v-model="dataForm.startTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="选择日期">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="车牌号:">
        <el-input v-model="dataForm.name" placeholder="车牌号" clearable></el-input>
      </el-form-item>
      <el-form-item label="运输货物名称:">
        <el-input v-model="dataForm.name" placeholder="运输货物名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-upload
          class="inline-block"
          action="https://jsonplaceholder.typicode.com/posts/">
          <el-button type="warning">批量导入</el-button>
        </el-upload>
        <!--<el-button v-if="isAuth('sys:user:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>-->
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        width="80"
        label="ID">
      </el-table-column>
      <el-table-column
        prop="agencyName"
        align="center"
        label="进厂时间">
      </el-table-column>
      <el-table-column
        prop="dataAmount"
        header-align="center"
        align="center"
        label="出厂时间">
      </el-table-column>
      <el-table-column
        prop="effectiveData"
        header-align="center"
        align="center"
        label="照片">
        <img class="table-list-img" src="" alt="">
      </el-table-column>
      <el-table-column
        prop="todayConsumeMoney"
        header-align="center"
        align="center"
        label="车牌号">
      </el-table-column>
      <el-table-column
        prop="effective"
        header-align="center"
        align="center"
        label="注册日期">
        <!--<template slot-scope="scope">-->
        <!--{{ (scope.row.effective).toFixed(2)*100+"%"}}-->
        <!--</template>-->
      </el-table-column>
      <el-table-column
        prop="agencyName"
        align="center"
        label="车辆识别代号(VIN)">
      </el-table-column>
      <el-table-column
        prop=""
        align="center"
        label="发动机号码">
      </el-table-column>
      <el-table-column
        prop="dataAmount"
        header-align="center"
        align="center"
        label="排放阶段">
      </el-table-column>
      <el-table-column
        prop="effectiveData"
        header-align="center"
        align="center"
        label="随车清单">
      </el-table-column>
      <el-table-column
        prop="todayConsumeMoney"
        header-align="center"
        align="center"
        label="行驶证">
      </el-table-column>
      <el-table-column
        prop="agencyName"
        align="center"
        label="运输货物名称">
      </el-table-column>
      <el-table-column
        prop=""
        align="center"
        label="运输量(吨)">
      </el-table-column>
      <el-table-column
        prop="dataAmount"
        header-align="center"
        align="center"
        label="车队名称">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button v-if="isAuth('biz:pdbaidudate:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button v-if="isAuth('biz:pdbaidudate:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './vehicle-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          startTime: '',
          endTime: '',
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList();
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/biz/pdbaidudata/list'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNum': this.pageIndex,
            'pageSize': this.pageSize,
            'agencyId': this.dataForm.agencyId,
            'startTime': this.dataForm.startTime,
            'endTime': this.dataForm.endTime
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            this.dataList = data.data.list
            this.totalPage = data.data.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var userIds = id ? [id] : this.dataListSelections.map(item => {
          return item.userId
        })
        this.$confirm(`确认删除该条数据吗?删除后数据不可恢复`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/biz/pdbaidudata/delete'),
            method: 'post',
            data: this.$http.adornData(userIds, false)
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }).catch(() => {})
      }
    }
  }
</script>
<style>
  .inline-block{
    display: inline-block;
  }
</style>
