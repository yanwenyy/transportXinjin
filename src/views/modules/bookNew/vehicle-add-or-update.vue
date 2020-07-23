<template>
  <el-dialog
    :title="!dataForm.id ? '新增' :look=='look'?'查看': '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="进厂时间">
        <el-date-picker
          v-model="dataForm.dataTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="进厂过磅时间">
        <el-date-picker
          v-model="dataForm.dataTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="出厂过磅时间">
        <el-date-picker
          v-model="dataForm.dataTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="出厂时间">
        <el-date-picker
          v-model="dataForm.dataTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="照片">
        <!--<div class="inline-block box-img" v-for="item in srcList">-->
          <!--<el-image class="look-img"-->
                    <!--:src="item"-->
                    <!--:preview-src-list="srcList">-->
          <!--</el-image>-->
          <!--<i class="el-icon-error box-img-del"></i>-->
        <!--</div>-->
        <el-upload
          action=""
          list-type="picture-card"
          :auto-upload="false"
          :on-change="imgChange"
          :on-remove="handleRemove" >
          <i class="el-icon-plus"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="门岗名称">
        <el-input v-model="dataForm.effectiveData" placeholder="门岗名称"></el-input>
      </el-form-item>
      <el-form-item label="磅房名称">
        <el-input v-model="dataForm.effectiveData" placeholder="磅房名称"></el-input>
      </el-form-item>
      <el-form-item label="车牌号">
        <el-input v-model="dataForm.effectiveData" placeholder="车牌号"></el-input>
      </el-form-item>
      <el-form-item label="注册日期">
        <el-date-picker
          v-model="dataForm.dataTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="车辆识别代号(VIN)">
        <el-input v-model="dataForm.todayConsumeMoney" placeholder="车辆识别代号(VIN)"></el-input>
      </el-form-item>
      <el-form-item label="发动机号码">
        <el-input v-model="dataForm.todayConsumeMoney" placeholder="发动机号码"></el-input>
      </el-form-item>
      <el-form-item label="燃油种类">
        <el-select v-model="value" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="随车清单">
        <!--<el-image v-for="item in scjd"-->
                  <!--class="look-img"-->
                  <!--:src="item"-->
                  <!--:preview-src-list="scjd">-->
        <!--</el-image>-->
        <el-upload
          action=""
          list-type="picture-card"
          :auto-upload="false"
          :on-change="imgChange"
          :on-remove="handleRemove" >
          <i class="el-icon-plus"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="行驶证">
        <el-upload
          action=""
          list-type="picture-card"
          :auto-upload="false"
          :on-change="imgChange"
          :on-remove="handleRemove" >
          <i class="el-icon-plus"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="排放阶段">
        <el-select v-model="value" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="供应商">
        <el-input v-model="dataForm.todayConsumeMoney" placeholder="供应商"></el-input>
      </el-form-item>
      <el-form-item label="物料编码">
        <el-input v-model="dataForm.dataAmount" placeholder="物料编码"></el-input>
      </el-form-item>
      <el-form-item label="物料名称">
        <el-input v-model="dataForm.effectiveData" placeholder="物料名称"></el-input>
      </el-form-item>
      <el-form-item label="计量单号">
        <el-input v-model="dataForm.effectiveData" placeholder="物料名称"></el-input>
      </el-form-item>
      <el-form-item label="毛重">
        <el-input v-model="dataForm.effectiveData" placeholder="物料名称"></el-input>
      </el-form-item>
      <el-form-item label="皮重">
        <el-input v-model="dataForm.effectiveData" placeholder="物料名称"></el-input>
      </el-form-item>
      <el-form-item label="净重">
        <el-input v-model="dataForm.effectiveData" placeholder="物料名称"></el-input>
      </el-form-item>
      <el-form-item label="集装箱号">
        <el-input v-model="dataForm.effectiveData" placeholder="物料名称"></el-input>
      </el-form-item>
      <el-form-item label="运输方式（铁路/公路）">
        <el-input v-model="dataForm.effectiveData" placeholder="物料名称"></el-input>
      </el-form-item>
      <el-form-item label="运输单位">
        <el-input v-model="dataForm.effectiveData" placeholder="物料名称"></el-input>
      </el-form-item>
    </el-form>
    <span v-if="look!='look'" slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import { isInteger } from '@/utils/validate';
  import {getBase64} from '@/utils'
  export default {
    data () {
      var validateInteger = (rule, value, callback) => {
        if(value===''){
          callback(new Error('不能为空'))
        }else if (!isInteger(value)) {
          callback(new Error('格式不正确'))
        } else {
          callback()
        }
      };
      var validateMoney = (rule, value, callback) => {
        if(!value){
          callback(new Error('不能为空'))
        }else if (!/(^[1-9]([0-9]+)?(\.[0-9]{1,2})?$)|(^(0){1}$)|(^[0-9]\.[0-9]([0-9])?$)/.test(value)) {
          callback(new Error('金额格式不正确'))
        } else {
          callback()
        }
      };
      return {
        scjd:[],
        visible: false,
        dialogImageUrl: '',
        dialogVisible: false,
        look:'',
        dataForm: {
          id: 0,
          dataTime: '',
          dataAmount: '',
          effectiveData: '',
          todayConsumeMoney: ''
        },
        options: [{
          value: '选项1',
          label: '国 0:0'
        }, {
          value: '选项2',
          label: '国 1:1'
        }, {
          value: '选项3',
          label: '国 2:2'
        }, {
          value: '选项4',
          label: '国 3:3'
        }, {
          value: '选项5',
          label: '国 4:4'
        }, {
          value: '选项6',
          label: '国 5:5'
        }, {
          value: '选项7',
          label: '国 6:6'
        }, {
          value: '选项8',
          label: '电动:D'
        }],
        value: '',
        dataRule: {
          dataTime: [
            { required: true, message: '数据时间不能为空', trigger: 'blur' }
          ],
          dataAmount: [
            { required: true,validator: validateInteger, trigger: 'blur' }
          ],
          effectiveData: [
            { required: true, validator: validateInteger,trigger: 'blur' }
          ],
          todayConsumeMoney: [
            { required: true, validator: validateMoney, trigger: 'blur' }
          ],
        }
      }
    },
    methods: {
      init (id,look) {
        console.log(id)
        this.dataForm.id = id||0;
        this.look=look;
        this.visible = true;
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields();
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/biz/pdbaidudata/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.dataTime = data.data.dataTime;
                this.dataForm.dataAmount = data.data.dataAmount;
                this.dataForm.effectiveData = data.data.effectiveData;
                this.dataForm.todayConsumeMoney = data.data.todayConsumeMoney;
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/biz/pdbaidudata/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'dataTime': this.dataForm.dataTime+" 00:00:00",
                'dataAmount': this.dataForm.dataAmount,
                'effectiveData': this.dataForm.effectiveData,
                'todayConsumeMoney': this.dataForm.todayConsumeMoney
              })
            }).then(({data}) => {
              if (data && data.code ===200) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      handleRemove(file, fileList) {
        console.log(file, fileList);
        getBase64(file.raw).then(res => {
          this.scjd.remove(res);
          console.log(this.scjd)
        })
      },
      handlePictureCardPreview(file) {
        this.dialogImageUrl = file.url;
        this.dialogVisible = true;
      },
      imgChange(file, fileList){
        getBase64(file.raw).then(res => {
          this.scjd.push(res);
        })
      }
    }
  }
</script>
<style scoped>
  >>> .el-form-item__label{
    width: 120px!important;
  }
  >>> .el-input{
    width: 90%;
  }
  >>>.look-img{
    width: 100px;
    height: 100px;
    margin-right: 10px;
  }
</style>

