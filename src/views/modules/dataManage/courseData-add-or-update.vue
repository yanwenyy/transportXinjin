<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="报名时间" prop="dataTime">
        <el-date-picker
          v-model="dataForm.dataTime"
          type="date"
          value-format="yyyy-MM-dd"
          placeholder="请选择时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="报名人数" prop="enterNum">
        <el-input type="number" min="0" v-model="dataForm.enterNum" placeholder="全款人数"></el-input>
      </el-form-item>
      <el-form-item label="去年同日报名人数" prop="lastYearEnterNum">
        <el-input type="number" min="0" v-model="dataForm.lastYearEnterNum" placeholder="全款人数"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import commonDate from '@/utils/formatDate'
  import { isEmail, isMobile } from '@/utils/validate'
  export default {
    data () {
      return {
        visible: false,
        roleList: [],
        dataForm: {
          id: 0,
          dataTime: '',
          enterNum: '',
          lastYearEnterNum: '',
        },
        dataRule: {
          dataTime: [
            { required: true, message: '数据时间不能为空', trigger: 'blur' }
          ],
          enterNum: [
            { required: true, message: '报名人数不能为空', trigger: 'blur' }
          ],
          lastYearEnterNum: [
            { required: true, message: '去年同日报名人数不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/biz/pdculturallessons/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.dataTime = data.data.dataTime;
                this.dataForm.enterNum = data.data.enterNum;
                this.dataForm.lastYearEnterNum = data.data.lastYearEnterNum;
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
              url: this.$http.adornUrl(`/biz/pdculturallessons/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'dataTime': this.dataForm.dataTime+" 00:00:00",
                'enterNum': this.dataForm.enterNum,
                'lastYearEnterNum': this.dataForm.lastYearEnterNum
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
    width: 150px!important;
  }
  >>> .el-input{
    width: 85%;
  }
</style>
