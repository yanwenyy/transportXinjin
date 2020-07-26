<template>
  <el-dialog
    title="查看"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
       <el-form-item label="物料名称">
        <el-input readonly v-model="msg.materialsName" placeholder="物料名称"></el-input>
      </el-form-item>
      <el-form-item label="汽车运输量(万t)">
        <el-input readonly v-model="msg.carWeigh" placeholder="汽车运输量(万t)"></el-input>
      </el-form-item>
      <el-form-item label="火车运输量(万t)">
        <el-input readonly v-model="msg.sumWeigh-msg.carWeigh" placeholder="火车运输量(万t)"></el-input>
      </el-form-item>
      <el-form-item label="总运输量(万t)">
        <el-input readonly v-model="msg.sumWeigh" placeholder="总运输量(万t)"></el-input>
      </el-form-item>
      <el-form-item label="清洁运输占比(%)">
        <el-input readonly v-model="((msg.sumWeigh-msg.carWeigh)/ msg.sumWeigh).toFixed(2)*100" placeholder="清洁运输占比(%)"></el-input>
      </el-form-item>
    </el-form>
    <!--<span slot="footer" class="dialog-footer">-->
      <!--<el-button @click="visible = false">取消</el-button>-->
      <!--&lt;!&ndash;<el-button type="primary" @click="dataFormSubmit()">确定</el-button>&ndash;&gt;-->
    <!--</span>-->
  </el-dialog>
</template>

<script>
  import { isInteger } from '@/utils/validate'
  export default {
    data () {
      return {
        visible: false,
        roleList: [],
        dialogImageUrl: '',
        dialogVisible: false,
        dataForm: {
          id: 0
        },
        msg:{},
        cleanPercent:'',
        dataRule: {

        }
      }
    },
    methods: {
      init (val) {
        this.msg=val;
        this.visible = true;
      },
      // 表单提交
      dataFormSubmit () {

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

