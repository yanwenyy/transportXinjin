<template>
  <el-dialog
    :title="!dataForm.id ? '新增' :look=='look'?'查看': '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="进厂时间">
        <el-date-picker
          v-model="dataForm.enterTime"
          type="datetime"
          value-format="yyyy-MM-dd HH:mm:ss"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="计量时间">
        <el-date-picker
          v-model="dataForm.weighTime"
          type="datetime"
          value-format="yyyy-MM-dd HH:mm:ss"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="退卡时间">
        <el-date-picker
          v-model="dataForm.checkOutTime"
          type="datetime"
          value-format="yyyy-MM-dd HH:mm:ss"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="出厂时间">
        <el-date-picker
          v-model="dataForm.outFactoryTime"
          type="datetime"
          value-format="yyyy-MM-dd HH:mm:ss"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="进厂照片">
      <div class="inline-block box-img">
        <div class="inline-block box-img" v-if="dataForm.enterImg&&dataForm.enterImg!=''">
          <div  v-for="item in dataForm.enterImg" class="inline-block img-list-div">
            <el-image class="look-img" title="点击查看大图"
                      :src="item.indexOf('http')!=-1?item:imgUrlfront+item" :preview-src-list="srcList" >
            </el-image>
            <i class="el-icon-error box-img-del" @click="handleRemove3(item)"></i>
          </div>
        </div>
        <div class="inline-block box-img">
          <el-upload
            :show-file-list="false"
            :headers="{'token':token}"
            :action="this.$http.adornUrl('/jinding/file/upload')"
            :on-success="handleChange3"
            :on-error="handleChange3"
            list-type="picture-card">
            <i class="el-icon-plus"></i>
          </el-upload>
        </div>
      </div>
    </el-form-item>
      <el-form-item label="出厂照片">
        <div class="inline-block box-img" v-if="dataForm.outImg&&dataForm.outImg!=''">
          <div  v-for="item in dataForm.outImg" class="inline-block img-list-div">
            <el-image class="look-img" title="点击查看大图"
                      :src="item.indexOf('http')!=-1?item:imgUrlfront+item" :preview-src-list="srcList" >
            </el-image>
            <i class="el-icon-error box-img-del" @click="handleRemove4(item)"></i>
          </div>
        </div>
        <div class="inline-block box-img">
          <el-upload
            :show-file-list="false"
            :headers="{'token':token}"
            :action="this.$http.adornUrl('/jinding/file/upload')"
            :on-success="handleChange4"
            :on-error="handleChange4"
            list-type="picture-card">
            <i class="el-icon-plus"></i>
          </el-upload>
        </div>
      </el-form-item>
      <el-form-item label="门岗名称">
        <el-input v-model="dataForm.doorPostName" placeholder="门岗名称"></el-input>
      </el-form-item>
      <el-form-item label="磅房名称">
        <el-input v-model="dataForm.poundRoom" placeholder="磅房名称"></el-input>
      </el-form-item>
      <el-form-item label="车牌号">
        <el-input v-model="dataForm.carNum" placeholder="车牌号"></el-input>
      </el-form-item>
      <el-form-item label="注册日期">
        <el-date-picker
          v-model="dataForm.registTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="车辆识别代号(VIN)">
        <el-input v-model="dataForm.vehicleNum" placeholder="车辆识别代号(VIN)"></el-input>
      </el-form-item>
      <el-form-item label="发动机号码">
        <el-input v-model="dataForm.engineNum" placeholder="发动机号码"></el-input>
      </el-form-item>
      <el-form-item label="燃油种类">
        <el-select v-model="dataForm.fuelType" placeholder="请选择">
          <el-option
            v-for="item in ryzl"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="随车清单">
        <div class="inline-block box-img" v-if="dataForm.carCheckList&&dataForm.carCheckList!=''">
          <el-image class="look-img" title="点击查看大图"
                    :src="dataForm.carCheckList.indexOf('http')!=-1?dataForm.carCheckList:imgUrlfront+dataForm.carCheckList" :preview-src-list="srcList" >
          </el-image>
          <i class="el-icon-error box-img-del" @click="dataForm.carCheckList=''"></i>
        </div>
        <div class="inline-block box-img"  v-if="dataForm.carCheckList==''||!dataForm.carCheckList">
          <el-upload
            :show-file-list="!dataForm.id&& dataForm.carCheckList==''"
            :headers="{'token':token}"
            :action="this.$http.adornUrl('/jinding/file/upload')"
            :on-success="handleChange"
            :on-error="handleChange"
            list-type="picture-card"
            :on-remove="handleRemove">
            <i class="el-icon-plus"></i>
          </el-upload>
        </div>
      </el-form-item>
      <el-form-item label="行驶证">
        <div class="inline-block box-img" v-if="dataForm.drivinglLicense&&dataForm.drivinglLicense!=''">
          <el-image class="look-img" title="点击查看大图"
                    :src="dataForm.drivinglLicense.indexOf('http')!=-1?dataForm.drivinglLicense:imgUrlfront+dataForm.drivinglLicense" :preview-src-list="srcList">
          </el-image>
          <i class="el-icon-error box-img-del" @click="dataForm.drivinglLicense=''"></i>
        </div>
        <div class="inline-block box-img" v-if="dataForm.drivinglLicense==''||!dataForm.drivinglLicense">
          <el-upload
            :show-file-list="!dataForm.id && dataForm.drivinglLicense==''"
            :headers="{'token':token}"
            :action="this.$http.adornUrl('/jinding/file/upload')"
            :on-success="handleChange2"
            :on-error="handleChange2"
            list-type="picture-card"
            :on-remove="handleRemove2">
            <i class="el-icon-plus"></i>
          </el-upload>
        </div>
      </el-form-item>
      <el-form-item label="排放标准">
        <el-select v-model="dataForm.emissionStand" placeholder="请选择">
          <el-option
            v-for="item in pfbz"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="供应商">
        <el-input v-model="dataForm.clientName" placeholder="供应商"></el-input>
      </el-form-item>
      <el-form-item label="物料编码">
        <el-input v-model="dataForm.materialsNum" placeholder="物料编码"></el-input>
      </el-form-item>
      <el-form-item label="物料名称">
        <el-input v-model="dataForm.materialsName" placeholder="物料名称"></el-input>
      </el-form-item>
      <el-form-item label="计量单号">
        <el-input v-model="dataForm.measureNum" placeholder="计量单号"></el-input>
      </el-form-item>
      <el-form-item label="毛重">
        <el-input v-model="dataForm.crossWeigh" placeholder="毛重"></el-input>
      </el-form-item>
      <el-form-item label="皮重">
        <el-input v-model="dataForm.tareWeigh" placeholder="皮重"></el-input>
      </el-form-item>
      <el-form-item label="净重">
        <el-input v-model="dataForm.netWeigh" placeholder="净重"></el-input>
      </el-form-item>
      <el-form-item label="集装箱号">
        <el-input v-model="dataForm.containerNum" placeholder="集装箱号"></el-input>
      </el-form-item>
      <el-form-item label="运输方式">
        <el-select v-model="dataForm.tranType" placeholder="请选择">
          <el-option
            v-for="item in ysfs"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="运输单位">
        <el-input v-model="dataForm.transportUnit" placeholder="运输单位"></el-input>
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
          enterTime: '',
          weighTime: '',
          checkOutTime: '',
          outFactoryTime: '',
          enterImg: [],
          outImg:[],
          doorPostName: '',
          poundRoom: '',
          carNum: '',
          registTime: '',
          vehicleNum: '',
          engineNum: '',
          fuelType: '',
          carCheckList: '',
          drivinglLicense: '',
          emissionStand: '',
          clientName: '',
          materialsNum: '',
          materialsName: '',
          measureNum: '',
          crossWeigh: '',
          tareWeigh: '',
          netWeigh: '',
          containerNum: '',
          tranType: '',
          transportUnit: '',
        },
        token:'',
        imgUrlfront:'',
        srcList: [],
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
            value: '国五',
            label: '国五'
          }, {
            value: '国六',
            label: '国六'
          }],
        ysfs:[
          {
            value: "0",
            label: '铁路'
          },
          {
            value:"1",
            label: '公路'
          }
        ],
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
        this.imgUrlfront=this.$http.adornUrl('/jinding/showImg/');
        this.token=this.$cookie.get('token');
        this.dataForm.id = id||0;
        this.look=look;
        this.visible = true;
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields();
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/biz/tran/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.enterTime = data.data.enterTime;
                this.dataForm.weighTime = data.data.weighTime;
                this.dataForm.checkOutTime = data.data.checkOutTime;
                this.dataForm.outFactoryTime = data.data.outFactoryTime;
                this.dataForm.enterImg = data.data.enterImg?data.data.enterImg.split(","):[];
                this.dataForm.outImg = data.data.outImg?data.data.outImg.split(","):[];
                this.dataForm.doorPostName = data.data.doorPostName;
                this.dataForm.poundRoom = data.data.poundRoom;
                this.dataForm.carNum = data.data.carNum;
                this.dataForm.registTime = data.data.registTime;
                this.dataForm.vehicleNum = data.data.vehicleNum;
                this.dataForm.engineNum = data.data.engineNum;
                this.dataForm.fuelType = data.data.fuelType;
                this.dataForm.carCheckList = data.data.carCheckList;
                this.dataForm.drivinglLicense = data.data.drivinglLicense;
                this.dataForm.emissionStand = data.data.doorEmissionStand;
                this.dataForm.clientName = data.data.clientName;
                this.dataForm.materialsNum = data.data.materialsNum;
                this.dataForm.materialsName = data.data.materialsName;
                this.dataForm.measureNum = data.data.measureNum;
                this.dataForm.crossWeigh = data.data.crossWeigh;
                this.dataForm.tareWeigh = data.data.tareWeigh;
                this.dataForm.netWeigh = data.data.netWeigh;
                this.dataForm.containerNum = data.data.containerNum;
                this.dataForm.tranType = data.data.tranType;
                this.dataForm.transportUnit = data.data.transportUnit;
                var list=this.dataForm.enterImg.concat(this.dataForm.outImg);
                this.dataForm.carCheckList&&list.push(this.dataForm.carCheckList);
                this.dataForm.drivinglLicense&&list.push(this.dataForm.drivinglLicense);
                var i=0,len=list.length;
                for(;i< len;i++){
                  var v=list[i];
                  list[i]=(list[i].indexOf('http')!=-1)?list[i]:this.imgUrlfront+list[i];
                }
                this.srcList=list;
                // console.log(this.srcList)
              }
            })
          }else{
            this.dataForm.enterTime = '';
            this.dataForm.weighTime = '';
            this.dataForm.checkOutTime ='';
            this.dataForm.outFactoryTime = '';
            this.dataForm.enterImg = [];
            this.dataForm.outImg =[];
            this.dataForm.doorPostName = '';
            this.dataForm.poundRoom = '';
            this.dataForm.carNum = '';
            this.dataForm.registTime = '';
            this.dataForm.vehicleNum ='';
            this.dataForm.engineNum = '';
            this.dataForm.fuelType = '';
            this.dataForm.carCheckList = '';
            this.dataForm.drivinglLicense = '';
            this.dataForm.emissionStand = '';
            this.dataForm.clientName = '';
            this.dataForm.materialsNum = '';
            this.dataForm.materialsName = '';
            this.dataForm.measureNum = '';
            this.dataForm.crossWeigh = '';
            this.dataForm.tareWeigh = '';
            this.dataForm.netWeigh = '';
            this.dataForm.containerNum = '';
            this.dataForm.tranType = '';
            this.dataForm.transportUnit = '';
            this.dataForm.srcList=[];
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/biz/tran/tran/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'enterTime': this.dataForm.enterTime,
                'weighTime': this.dataForm.weighTime,
                'checkOutTime':this.dataForm.checkOutTime,
                'outFactoryTime': this.dataForm.outFactoryTime,
                'enterImg': this.dataForm.enterImg.join(","),
                'outImg': this.dataForm.outImg.join(","),
                'doorPostName': this.dataForm.doorPostName,
                'poundRoom': this.dataForm.poundRoom,
                'carNum': this.dataForm.carNum,
                'registTime': !this.dataForm.id ||this.dataForm.registTime.indexOf("00:00:00")==-1? this.dataForm.registTime+" 00:00:00":this.dataForm.registTime,
                'vehicleNum': this.dataForm.vehicleNum,
                'engineNum': this.dataForm.engineNum,
                'fuelType': this.dataForm.fuelType,
                'carCheckList': this.dataForm.carCheckList,
                'drivinglLicense': this.dataForm.drivinglLicense,
                'emissionStand': this.dataForm.emissionStand,
                'clientName': this.dataForm.clientName,
                'materialsNum': this.dataForm.materialsNum,
                'materialsName': this.dataForm.materialsName,
                'measureNum': this.dataForm.measureNum,
                'crossWeigh': this.dataForm.crossWeigh,
                'tareWeigh': this.dataForm.tareWeigh,
                'netWeigh': this.dataForm.netWeigh,
                'containerNum': this.dataForm.containerNum,
                'tranType': this.dataForm.tranType,
                'transportUnit': this.dataForm.transportUnit,
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
      //上传图片
      handleRemove(file, fileList) {
        this.dataForm.carCheckList='';
      },
      handleChange(response, file, fileList){
        if (response && response.code === 10000) {
          this.$message({
            message: '上传成功',
            type: 'success',
            duration: 1500,
            onClose: () => {
              this.dataForm.carCheckList=response.data;
            }
          })
        } else {
          this.$message.error(response.msg)
        }
      },
      handleChange(response, file, fileList){
        if (response && response.code === 10000) {
          this.$message({
            message: '上传成功',
            type: 'success',
            duration: 1500,
            onClose: () => {
              this.dataForm.carCheckList=response.data;
            }
          })
        } else {
          this.$message.error(response.msg)
        }
      },
      handleRemove2(file, fileList) {
        this.dataForm.drivinglLicense='';
      },
      handleChange2(response, file, fileList){
        if (response && response.code === 10000) {
          this.$message({
            message: '上传成功',
            type: 'success',
            duration: 1500,
            onClose: () => {
              this.dataForm.drivinglLicense=response.data;
            }
          })
        } else {
          this.$message.error(response.msg)
        }
      },
      handleRemove3(file) {
        this.dataForm.enterImg.remove(file);
      },
      handleChange3(response, file, fileList){
        if (response && response.code === 10000) {
          this.$message({
            message: '上传成功',
            type: 'success',
            duration: 1500,
            onClose: () => {
              this.dataForm.enterImg.push(response.data);
            }
          })
        } else {
          this.$message.error(response.msg)
        }
      },
      handleRemove4(file) {
        this.dataForm.outImg.remove(file);
        console.log(this.dataForm.outImg)
      },
      handleChange4(response, file, fileList){
        if (response && response.code === 10000) {
          this.$message({
            message: '上传成功',
            type: 'success',
            duration: 1500,
            onClose: () => {
              this.dataForm.outImg.push(response.data);

            }
          })
        } else {
          this.$message.error(response.msg)
        }
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
    width: 148px;
    height: 148px;
    margin-right: 10px;
  }
  .box-img{
    vertical-align: top;
  }
  >>>.img-list-div{
    position: relative;
    vertical-align: top;
  }
</style>

