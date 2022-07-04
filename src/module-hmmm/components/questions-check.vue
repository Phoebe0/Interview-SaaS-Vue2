<template>
  <div class='container'>
    <el-dialog
      title="题目审核"
      :visible="dialogVisible"
      width="30%"
      @close="dialogVisible=false">
      <el-form :model="checkForm" :rules="rules" ref="ruleForm">
        <el-form-item prop="chkState">
          <el-radio v-model="checkForm.chkState" :label="1" style="margin:10px 20px 10px 10px">通过</el-radio>
          <el-radio v-model="checkForm.chkState" :label="2">拒绝</el-radio>
        </el-form-item>
        <br>
        <el-form-item prop="chkRemarks">
          <el-input
          type="textarea"
          autosize
          :rows="2"
          placeholder="请输入内容"
          v-model="checkForm.chkRemarks"
          style="margin: 10px 0">
        </el-input>
        </el-form-item>

      </el-form>
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addChk">确 定</el-button>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'QuestionCheck',
  props: {
    checkForm: {
      type: Object,
      required: true
    }
  },

  data () {
    return {
      radio: '1',
      rules: {
        chkRemarks: [
          { required: true, message: '请输入学科名称', trigger: 'blur' }
        ]
      },
      dialogVisible: false,
      chkRemarks: '' // 审核意见
    }
  },
  methods: {
    // 弹出对话框
    showCheckDialog () {
      this.dialogVisible = true
      this.$emit('showCheckDialog')
    },
    closeDialog () {
      this.dialogVisible = false
    },
    // 添加审核
    addChk () {
      this.$emit('addChk')
      this.dialogVisible = false
    }

  }
}
</script>

<style scoped lang='less'></style>
