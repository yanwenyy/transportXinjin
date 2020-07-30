<template>
  <div class="mod-user">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item label="门岗:">
        <el-input v-model="dataForm.doorPostName" placeholder="门岗" clearable></el-input>
      </el-form-item>
      <el-form-item label="磅房:">
        <el-input v-model="dataForm.poundRoom" placeholder="磅房" clearable></el-input>
      </el-form-item>
      <el-form-item label="集装箱号:">
        <el-input v-model="dataForm.containerNum" placeholder="集装箱号" clearable></el-input>
      </el-form-item>
      <el-form-item label="运输方式:">
        <el-select clearable  v-model="dataForm.tranType" placeholder="请选择">
          <el-option
            v-for="item in ysfs"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="燃油种类:">
        <el-select clearable  v-model="dataForm.fuelType" placeholder="请选择">
          <el-option
            v-for="item in ryzl"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="排放标准:">
        <el-select clearable  v-model="dataForm.emissionStand" placeholder="请选择">
          <el-option
            v-for="item in pfbz"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item v-show="searchMore" label="进厂时间:">
        <el-date-picker
          v-model="dataForm.enterTime"
          type="datetime"
          value-format="yyyy-MM-dd HH:mm:ss"
          placeholder="选择日期">
        </el-date-picker>
      </el-form-item>
      <el-form-item v-show="searchMore" label="出厂时间:">
        <el-date-picker
          v-model="dataForm.outFactoryTime"
          type="datetime"
          value-format="yyyy-MM-dd HH:mm:ss"
          placeholder="选择日期">
        </el-date-picker>
      </el-form-item>
      <el-form-item v-show="searchMore" label="车牌号:">
        <el-input v-model="dataForm.carNum" placeholder="车牌号" clearable></el-input>
      </el-form-item>
      <el-form-item v-show="searchMore" label="运输货物名称:">
        <el-input v-model="dataForm.materialsName" placeholder="运输货物名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <span class="showMore" @click="searchMore=!searchMore">{{searchMore?'收起':'显示更多'}}</span><el-button @click="getDataList()">查询</el-button>
        <el-button v-if="" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-upload
          class="inline-block"
          :headers="{'token':token}"
          :action="this.$http.adornUrl('/biz/tran/import/tran/car')"
          :on-success="handleChange"
          :on-error="handleChange"
          :show-file-list="false"
        >
          <el-button type="warning">批量导入</el-button>
        </el-upload>
        <el-button type="warning" @click="down">导出</el-button>
        <el-popover
          placement="right"
          width="400"
          trigger="click">
          <template>
            <el-checkbox :indeterminate="isIndeterminate" v-model="checkAll" @change="handleCheckAllChange">全选</el-checkbox>
            <el-checkbox-group v-model="checkedCities" @change="handleCheckedCitiesChange">
              <el-checkbox class="showCheckbox" v-for="city in cities" :label="city" :key="city">{{city}}</el-checkbox>
            </el-checkbox-group>
          </template>
          <el-button slot="reference">批量隐藏列</el-button>
        </el-popover>
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
        v-if="checkedCities.indexOf('ID')!=-1"
        type="index"
        header-align="center"
        align="center"
        width="80"
        label="ID">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('进厂时间')!=-1"
        prop="enterTime"
        align="center"
        label="进厂时间">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('计量时间')!=-1"
        prop="weighTime"
        align="center"
        label="计量时间">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('退卡时间')!=-1"
        prop="checkOutTime"
        align="center"
        label="退卡时间">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('出厂时间')!=-1"
        prop="outFactoryTime"
        header-align="center"
        align="center"
        label="出厂时间">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('进厂照片')!=-1"
        header-align="center"
        align="center"
        label="进厂照片">
        <template slot-scope="scope">
          <img v-for="item in (scope.row.enterImg?scope.row.enterImg.split(','):[])" class="table-list-img" :src="item&&item.indexOf('http')!=-1?item:imgUrlfront+item" alt="">
        </template>
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('出厂照片')!=-1"
        header-align="center"
        align="center"
        label="出厂照片">
        <template slot-scope="scope">
          <img v-for="item in (scope.row.outImg?scope.row.outImg.split(','):[])" class="table-list-img" :src="(item&&item.indexOf('http')!=-1?item:imgUrlfront+item)" alt="">
        </template>
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('门岗名称')!=-1"
        prop="doorPostName"
        header-align="center"
        align="center"
        label="门岗名称">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('磅房名称')!=-1"
        prop="poundRoom"
        header-align="center"
        align="center"
        label="磅房名称">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('车牌号')!=-1"
        prop="carNum"
        header-align="center"
        align="center"
        label="车牌号">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('注册日期')!=-1"
        prop="registTime"
        header-align="center"
        align="center"
        label="注册日期">
        <!--<template slot-scope="scope">-->
        <!--{{ (scope.row.effective).toFixed(2)*100+"%"}}-->
        <!--</template>-->
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('车辆识别代号')!=-1"
        prop="vehicleNum"
        align="center"
        label="车辆识别代号(VIN)">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('发动机号码')!=-1"
        prop="engineNum"
        align="center"
        label="发动机号码">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('燃油种类')!=-1"
        prop="fuelType"
        align="center"
        label="燃油种类">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('随车清单')!=-1"
        header-align="center"
        align="center"
        label="随车清单">
        <template slot-scope="scope">
          <img class="table-list-img" :src="(scope.row.carCheckList&&scope.row.carCheckList.indexOf('http')!=-1?scope.row.carCheckList:scope.row.carCheckList?imgUrlfront+scope.row.carCheckList:'')" alt="">
        </template>
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('行驶证')!=-1"
        header-align="center"
        align="center"
        label="行驶证">
        <template slot-scope="scope">
          <img class="table-list-img" :src="(scope.row.drivinglLicense&&scope.row.drivinglLicense.indexOf('http')!=-1?scope.row.drivinglLicense:scope.row.drivinglLicense?imgUrlfront+scope.row.drivinglLicense:'')" alt="">
        </template>
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('排放阶段')!=-1"
        prop="emissionStand"
        header-align="center"
        align="center"
        label="排放阶段">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('供应商')!=-1"
        prop="clientName"
        align="center"
        label="供应商">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('物料编码')!=-1"
        prop="materialsNum"
        align="center"
        label="物料编码">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('物料名称')!=-1"
        prop="materialsName"
        align="center"
        label="物料名称">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('计量单号')!=-1"
        prop="measureNum"
        align="center"
        label="计量单号">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('毛重')!=-1"
        prop="crossWeigh"
        align="center"
        label="毛重">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('皮重')!=-1"
        prop="tareWeigh"
        header-align="center"
        align="center"
        label="皮重">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('净重')!=-1"
        prop="netWeigh"
        header-align="center"
        align="center"
        label="净重">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('集装箱号')!=-1"
        prop="containerNum"
        header-align="center"
        align="center"
        label="集装箱号">
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('运输方式')!=-1"
        header-align="center"
        align="center"
        label="运输方式（铁路/公路）">
        <template slot-scope="scope">
          {{scope.row.tranType==1?'公路':'铁路'}}
        </template>
      </el-table-column>
      <el-table-column
        v-if="checkedCities.indexOf('运输单位')!=-1"
        prop="transportUnit"
        header-align="center"
        align="center"
        label="运输单位">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <!--<el-button v-if="" type="text" size="small" @click="addOrUpdateHandle(scope.row.id,'look')">查看</el-button>-->
          <el-button v-if="" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button v-if="" type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
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
  const cityOptions = ['ID', '进厂时间', '计量时间', '退卡时间','出厂时间','进厂照片','出厂照片','门岗名称','磅房名称',
    '车牌号','注册日期','车辆识别代号','发动机号码','燃油种类','随车清单','行驶证','排放阶段','供应商',
  '物料编码','物料名称','计量单号','毛重','皮重','净重','集装箱号','运输方式','运输单位'];
  import AddOrUpdate from './vehicle-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          enterTime: '',
          outFactoryTime: '',
          carNum:'',
          materialsName:'',
          doorPostName:'',
          poundRoom: '',
          containerNum: '',
          tranType:'',
          emissionStand:'',
          fuelType:'',

        },
        searchMore:false,
        url:'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg',
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        checkAll: false,
        checkedCities: cityOptions,
        cities: cityOptions,
        isIndeterminate: true,
        imgUrlfront:'',
        token:'',
        ryzl:[
          {
            value: '柴油',
            label: '柴油'
          },
          {
            value: '天然气',
            label: '天然气'
          },
          {
            value: '纯电动',
            label: '纯电动'
          },
          {
            value: '油电混动',
            label: '油电混动'
          },
        ],
        pfbz: [
          {
          value: '国 0:0',
          label: '国 0:0'
        }, {
          value: '国 1:1',
          label: '国 1:1'
        }, {
          value: '国 2:2',
          label: '国 2:2'
        }, {
          value: '国 3:3',
          label: '国 3:3'
        }, {
          value: '国 4:4',
          label: '国 4:4'
        }, {
          value: '国 5:5',
          label: '国 5:5'
        }, {
          value: '国 6:6',
          label: '国 6:6'
        }, {
          value: '电动:D',
          label: '电动:D'
        }],
        ysfs:[
          {
            value: '0',
            label: '铁路'
          },
          {
            value: '1',
            label: '公路'
          }
        ]
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList();
      this.imgUrlfront=this.$http.adornUrl('/jinding/showImg/');
      this.token=this.$cookie.get('token')
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/jinding/tran/list'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNum': this.pageIndex,
            'pageSize': this.pageSize,
            'enterTime': this.dataForm.enterTime||'',
            'outFactoryTime': this.dataForm.outFactoryTime||'',
            'carNum': this.dataForm.carNum,
            'materialsName': this.dataForm.materialsName,
            'doorPostName': this.dataForm.doorPostName,
            'poundRoom': this.dataForm.poundRoom,
            'containerNum': this.dataForm.containerNum,
            'tranType': this.dataForm.tranType,
            'emissionStand': this.dataForm.emissionStand,
            'fuelType': this.dataForm.fuelType,

          })
        }).then(({data}) => {
          if (data && data.code === 10000) {
            this.dataList = data.data
            this.totalPage = data.total
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
      addOrUpdateHandle (id,look) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id,look)
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
            url: this.$http.adornUrl('/biz/tran/delete'),
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
      },


      //显示隐藏
      handleCheckAllChange(val) {
        this.checkedCities = val ? cityOptions : [];
        this.isIndeterminate = false;
      },
      handleCheckedCitiesChange(value) {
        let checkedCount = value.length;
        this.checkAll = checkedCount === this.cities.length;
        this.isIndeterminate = checkedCount > 0 && checkedCount < this.cities.length;
      },

      //导入
      handleChange(response, file, fileList){
        if (response && response.code === 10000) {
          this.$message({
            message: '导入成功',
            type: 'success',
            duration: 1500,
            onClose: () => {
              this.getDataList()
            }
          })
        } else {
          this.$message.error(response.msg)
        }
      },

      //导出
      down (){
        var url='/jinding/po/list?pageNum='+this.pageIndex+'&pageSize='+this.pageSize+'&enterTime='+this.dataForm.enterTime+'&outFactoryTime='+this.dataForm.outFactoryTime+ '&carNum='+this.dataForm.carNum+'&materialsName='+this.dataForm.materialsName+ '&doorPostName='+this.dataForm.doorPostName+'&poundRoom='+this.dataForm.poundRoom+'&containerNum='+ this.dataForm.containerNum+'&tranType='+this.dataForm.tranType+'&emissionStand='+this.dataForm.emissionStand+ '&fuelType='+this.dataForm.fuelType;
        window.open(this.$http.adornUrl(url));
      }
    }
  }
</script>
<style>
  .inline-block{
    display: inline-block;
  }
  .showCheckbox{
    width: 80px!important;
    display: inline-block;
    margin-left: 0!important;
  }
  .el-input{
    width: 150px;
  }
  .showMore{
    color:cornflowerblue;
    cursor: pointer;
    margin-right: 30px;
  }
  .table-list-img{
    margin-bottom: 10px;
  }
</style>
