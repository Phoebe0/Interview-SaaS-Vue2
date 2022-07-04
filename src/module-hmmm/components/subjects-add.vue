<template>
  <div class='container'>

      <el-dialog
        :title="title"
        :visible="dialogVisible"
        width="60%"
        @close="dialogVisible=false"
        >
          <el-form :model="form" :rules="rules" ref="ruleForm" label-width="100px">
            <el-form-item label="学科名称" prop="subjectName">
              <el-input v-model="form.subjectName"></el-input>
            </el-form-item>
            <!-- 开关 -->
            <el-form-item label="是否显示">
              <el-switch
              v-model="form.isFrontDisplay"
              active-value=1
              active-color="#13ce66"
              inactive-color="#ff4949">
            </el-switch>
            </el-form-item>
          </el-form>

        <template #footer>
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="addSubject">确 定</el-button>
        </template>
      </el-dialog>
  </div>
</template>

<script>

export default {
  name: 'SubjectsAdd',
  props: {
    title: {
      type: String,
      required: true
    },
    form: {
      type: Object,
      required: true
    }

  },
  data () {
    return {
      dialogVisible: false,

      rules: {
        subjectName: [
          { required: true, message: '请输入学科名称', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 1 到 10 个字符', trigger: 'blur' }
        ]
      }

    }
  },
  methods: {
    // 弹出对话框
    showDialog () {
      this.dialogVisible = true
      this.$emit('showDialog')
    },
    // 关闭对话框
    closeDialog () {
      this.dialogVisible = false
    },
    // 添加/修改
    addSubject () {
      this.$emit('addSubject')
    }
  }

}
</script>

<style scoped lang='less'></style>
