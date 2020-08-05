<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <div class="addClass" v-if="!dataForm.id">
      <el-radio v-model="radio" label="1">物料大类</el-radio>
      <el-radio v-model="radio" label="2">物料</el-radio>
    </div>
    <template v-if="radio==1&&!dataForm.id">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
        <el-form-item label="物料大类名称">
          <el-input v-model="dataForm.parentName" placeholder="物料大类名称"></el-input>
        </el-form-item>
        <el-form-item label="物料大类编码">
          <el-input v-model="dataForm.parentId" placeholder="物料大类编码"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="visible = false">取消</el-button>
        <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
      </span>
    </template>
    <template v-if="radio==2||dataForm.id">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
        <el-form-item label="物料大类">
          <el-select v-model="dataForm.parentId" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.id"
              :label="item.materialsName"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="物料名称">
          <el-input v-model="dataForm.materialsName" placeholder="物料名称"></el-input>
        </el-form-item>
        <el-form-item label="物料名称编码">
          <el-input v-model="dataForm.materialsId" placeholder="物料名称编码"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="visible = false">取消</el-button>
        <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script>
  import { isInteger } from '@/utils/validate'
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
        visible: false,
        dialogImageUrl: '',
        dialogVisible: false,
        radio:'1',
        dataForm: {
          id: 0,
          materialsId:'',
          materialsName: '',
          parentId: '',
          parentName: '',
        },
        options: [{
          value: '1',
          label: '物料1'
        }, {
          value: '2',
          label: '物料2'
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
    created(){
      this.dataForm.materialsId = "";
      this.dataForm.materialsName = "";
      this.dataForm.parentId = "";
      this.dataForm.parentName = "";
      this.$http({
        url: this.$http.adornUrl('/biz/materials/select/list'),
        method: 'get',
        params: this.$http.adornParams()
      }).then(({data}) => {
        this.options = data && data.code === 200 ? data.data : []
      })
    },
    methods: {
      init (id) {
        this.dataForm.id = id||0;
        this.visible = true;
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields();
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/biz/materials/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.materialsId = data.data.id;
                this.dataForm.materialsName = data.data.materialsName;
                this.dataForm.parentId = data.data.parentId;
                this.dataForm.parentName = data.data.parentName;
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
              url: this.$http.adornUrl(`/biz/materials/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data:this.radio==1 &&!this.dataForm.id?this.$http.adornData({
                'id': this.dataForm.parentId,
                'materialsName': this.dataForm.parentName,
                'type': '1',
              }):this.$http.adornData({
                'id': this.dataForm.materialsId,
                'materialsName': this.dataForm.materialsName,
                'parentId':this.dataForm.parentId,
                // 'parentName': this.dataForm.parentName,
                'type': '2',
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
      },
      handlePictureCardPreview(file) {
        this.dialogImageUrl = file.url;
        this.dialogVisible = true;
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
  .addClass{
    margin-bottom: 20px;
    padding-left: 20px;
    box-sizing: border-box;
  }
</style>

