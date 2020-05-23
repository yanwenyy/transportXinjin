<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="数据时间" prop="years">
        <el-date-picker
          v-model="dataForm.years"
          type="year"
          value-format="yyyy-MM-dd"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="招生计划人数" prop="studentNum">
        <el-input type="number" min="0" v-model="dataForm.studentNum" placeholder="招生计划人数"></el-input>
      </el-form-item>
      <h2 class="addTitle">渠道招生人数</h2>
      <el-form-item label="线上" prop="onlineNum">
        <el-input type="number" min="0" v-model="dataForm.onlineNum" placeholder="线上"></el-input>
      </el-form-item>
      <el-form-item label="地推" prop="pusnNum">
        <el-input type="number" min="0" v-model="dataForm.pusnNum" placeholder="地推"></el-input>
      </el-form-item>
      <el-form-item label="教学部" prop="edcactionNum">
        <el-input type="number" min="0" v-model="dataForm.edcactionNum" placeholder="教学部"></el-input>
      </el-form-item>
      <el-form-item label="画室" prop="studioNum">
        <el-input type="number" min="0" v-model="dataForm.studioNum" placeholder="画室"></el-input>
      </el-form-item>
      <el-form-item label="其他" prop="otherNum">
        <el-input v-model="dataForm.otherNum" placeholder="其他"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import { isInteger } from '@/utils/validate'
  export default {
    data () {
      var validateInteger = (rule, value, callback) => {
        if(value==''){
          callback(new Error('不能为空'))
        }else if (!isInteger(value)) {
          callback(new Error('人数格式不正确'))
        } else {
          callback()
        }
      };
      return {
        visible: false,
        roleList: [],
        dataForm: {
          id: 0,
          years:'',
          studentNum: '',
          onlineNum: '',
          pusnNum: '',
          edcactionNum: '',
          studioNum:'',
          otherNum: ''
        },
        dataRule: {
          years: [
            { required: true, message: '数据时间不能为空', trigger: 'blur' }
          ],studentNum: [
            { required: true,validator: validateInteger, trigger: 'blur' }
          ],onlineNum: [
            { required: true, validator: validateInteger, trigger: 'blur' }
          ],pusnNum: [
            { required: true, validator: validateInteger, trigger: 'blur' }
          ],edcactionNum: [
            { required: true, validator: validateInteger, trigger: 'blur' }
          ],studioNum: [
            { required: true, validator: validateInteger, trigger: 'blur' }
          ],otherNum: [
            { required: true, validator: validateInteger, trigger: 'blur' }
          ],

        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id||0;
        this.visible = true;
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields();
          if (this.dataForm.id) {
            this.$refs['dataForm'].resetFields();
            this.$http({
              url: this.$http.adornUrl(`/biz/pdenrollmentgoal/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.years = data.data.enrollmentYear;
                this.dataForm.studentNum = data.data.studentNum;
                this.dataForm.onlineNum = data.data.onlineNum;
                this.dataForm.pusnNum = data.data.pusnNum;
                this.dataForm.edcactionNum = data.data.edcactionNum;
                this.dataForm.studioNum = data.data.studioNum;
                this.dataForm.otherNum = data.data.otherNum;
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
              url: this.$http.adornUrl(`/biz/pdenrollmentgoal/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'years': this.dataForm.years.split("-")[0],
                'studentNum': Number(this.dataForm.studentNum),
                'onlineNum': Number(this.dataForm.onlineNum),
                'pusnNum': Number(this.dataForm.pusnNum),
                'edcactionNum': Number(this.dataForm.edcactionNum),
                'studioNum': Number(this.dataForm.studioNum),
                'otherNum': Number(this.dataForm.otherNum)
              })
            }).then(({data}) => {
              if (data && data.code === 200) {
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
</style>
