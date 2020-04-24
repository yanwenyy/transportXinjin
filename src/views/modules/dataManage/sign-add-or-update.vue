<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="数据时间" prop="dateTime">
        <el-date-picker
          v-model="dataForm.dateTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="全款人数" prop="fullMoneyNum">
        <el-input type="number" v-model="dataForm.fullMoneyNum" placeholder="全款人数"></el-input>
      </el-form-item>
      <el-form-item label="定金人数" prop="frontMoneyNum">
        <el-input type="number" v-model="dataForm.frontMoneyNum" placeholder="定金人数"></el-input>
      </el-form-item>
      <el-form-item label="定金转全款人数" prop="fronFullNum">
        <el-input type="number" v-model="dataForm.fronFullNum" placeholder="定金转全款人数"></el-input>
      </el-form-item>
      <el-form-item  label="去年同日定金转+全款总计人数" prop="lastYearNum">
        <el-input type="number" v-model="dataForm.lastYearNum"></el-input>
      </el-form-item>
      <h2 class="addTitle">渠道人数</h2>
      <el-form-item label="线上" prop="onlineNum">
        <el-input type="number" v-model="dataForm.onlineNum" placeholder="线上"></el-input>
      </el-form-item>
      <el-form-item label="地推" prop="pusnNum">
        <el-input type="number" v-model="dataForm.pusnNum" placeholder="地推"></el-input>
      </el-form-item>
      <el-form-item label="教学部" prop="edcactionNum">
        <el-input type="number" v-model="dataForm.edcactionNum" placeholder="教学部"></el-input>
      </el-form-item>
      <el-form-item label="画室" prop="studioNum">
        <el-input type="number" v-model="dataForm.studioNum" placeholder="画室"></el-input>
      </el-form-item>
      <el-form-item label="其他" prop="otherNum">
        <el-input v-model="dataForm.otherNum" placeholder="其他"></el-input>
      </el-form-item>
    </el-form>
    <span v-if="author==''" slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import { isEmail, isMobile } from '@/utils/validate'
  export default {
    data () {
      var validatePassword = (rule, value, callback) => {
        if (!this.dataForm.id && !/\S/.test(value)) {
          callback(new Error('密码不能为空'))
        } else {
          callback()
        }
      }
      var validateComfirmPassword = (rule, value, callback) => {
        if (!this.dataForm.id && !/\S/.test(value)) {
          callback(new Error('确认密码不能为空'))
        } else if (this.dataForm.password !== value) {
          callback(new Error('确认密码与密码输入不一致'))
        } else {
          callback()
        }
      }
      var validateEmail = (rule, value, callback) => {
        if (!isEmail(value)) {
          callback(new Error('邮箱格式错误'))
        } else {
          callback()
        }
      }
      var validateMobile = (rule, value, callback) => {
        if (!isMobile(value)) {
          callback(new Error('手机号格式错误'))
        } else {
          callback()
        }
      }
      return {
        visible: false,
        roleList: [],
        preYear:19,
        author:'',//查看
        dataForm: {
          id: 0,
          dateTime: '',
          fullMoneyNum: '',
          frontMoneyNum: '',
          fronFullNum: '',
          lastYearNum: '',
          onlineNum: '',
          pusnNum: '',
          edcactionNum: '',
          studioNum: '',
          otherNum: '',
        },
        dataRule: {
          dateTime: [
            { required: true, message: '数据时间不能为空', trigger: 'blur' }
          ],
          fullMoneyNum: [
            { required: true, message: '全款人数不能为空', trigger: 'blur' }
          ],
          frontMoneyNum: [
            { required: true, message: '定金人数不能为空', trigger: 'blur' }
          ],
          fronFullNum: [
            { required: true, message: '定金转全款人数不能为空', trigger: 'blur' }
          ],
          lastYearNum: [
            { required: true, message: '去年同日定金+全款人数不能为空', trigger: 'blur' }
          ],
          onlineNum: [
            { required: true, message: '线上不能为空', trigger: 'blur' }
          ],
          pusnNum: [
            { required: true, message: '地推不能为空', trigger: 'blur' }
          ],
          edcactionNum: [
            { required: true, message: '教学部不能为空', trigger: 'blur' }
          ],
          studioNum: [
            { required: true, message: '画室不能为空', trigger: 'blur' }
          ],
          otherNum: [
            { required: true, message: '其他不能为空', trigger: 'blur' }
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
            this.$http({
              url: this.$http.adornUrl(`/biz/pdsignup/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.dateTime = data.data.dateTime;
                this.dataForm.fullMoneyNum = data.data.fullMoneyNum;
                this.dataForm.frontMoneyNum = data.data.frontMoneyNum;
                this.dataForm.fronFullNum = data.data.fronFullNum;
                this.dataForm.lastYearNum = data.data.lastYearNum;
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
              url: this.$http.adornUrl(`/sys/user/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'agencyId': this.dataForm.id || undefined,
                'dateTime': this.dataForm.dateTime+" 00:00:00",
                'fullMoneyNum': this.dataForm.fullMoneyNum,
                'frontMoneyNum': this.dataForm.frontMoneyNum,
                'fronFullNum': this.dataForm.fronFullNum,
                'lastYearNum': this.dataForm.lastYearNum,
                'pusnNum': this.dataForm.pusnNum,
                'edcactionNum': this.dataForm.edcactionNum,
                'studioNum': this.dataForm.studioNum,
                'onlineNum': this.dataForm.onlineNum,
                'otherNum': this.dataForm.otherNum,
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
  .sign-add-span{
    display: inline-block;
    width: 120px;
  }
  >>> .el-form-item__label{
    width: 120px!important;
  }
  >>> .el-input{
    width: 90%;
  }
</style>
