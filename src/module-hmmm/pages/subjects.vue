<template>
  <div class='container-subjects'>
    <el-card>

      <!-- 搜索 -->
      <!-- 分为左右两块，用栅格 -->
      <el-row :gutter="20">
        <el-col :span="21">
          <div class="grid-content bg-purple">
             <!-- 搜索框 -->
            <el-form :inline="true" size="small" :model="subjectsFrom">
              <el-form-item label="学科名称">
                <el-input v-model="subjectsFrom.subjectName" placeholder="请输入内容"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button @click="reset" >清除</el-button>
                <el-button type="primary" @click="search">搜索</el-button>
              </el-form-item>
            </el-form>
          </div>
        </el-col>
        <el-col :span="3">
          <div class="grid-content bg-purple">
            <!-- 新增学科 -->
            <el-button
            type="success"
            size="small"
            icon="el-icon-edit"
            @click="showDialog"
            >
            新增学科
            </el-button>
          </div>
        </el-col>
      </el-row>

      <!-- 总数 -->
      <el-alert
        :title="`数据一共${total}条`"
        type="info"
        :closable="false"
        show-icon
        style="margin: 10px"
        >
      </el-alert>

      <!-- 表格 -->
      <el-table :data="subjectList" :header-cell-style="{background:'rgb(250, 250, 250)'}">
        <el-table-column label="序号" type="index" :index="indexMethod"></el-table-column>
        <el-table-column prop="subjectName" label="学科名称"></el-table-column>
        <el-table-column prop="username" label="创建者"></el-table-column>
        <el-table-column label="创建日期" width="200px">
          <template #default="{row}">
            {{row.addDate | parseTimeByString}}
          </template>
        </el-table-column>
        <el-table-column label="前台是否显示">
          <template #default="{row}">
            {{row.isFrontDisplay === 1 ? '是' : '否'}}
          </template>
        </el-table-column>
        <el-table-column prop="twoLevelDirectory" label="二级目录"></el-table-column>
        <el-table-column prop="tags" label="标签"></el-table-column>
        <el-table-column prop="totals" label="题目数量"></el-table-column>
        <el-table-column label="操作" width="250px">
          <template #default="{row}">
            <el-button type="text" @click="toDirectorys(row)">学科分类</el-button>
            <el-button type="text" @click="toTags(row)">学科标签</el-button>
            <el-button type="text" @click="editSubject(row.id)">修改</el-button>
            <el-button type="text" @click="delSubject(row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <!-- 分页 -->
      <el-pagination
      style="float:right; margin: 10px"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      background
      :page-size="subjectsFrom.pagesize"
      :current-page="subjectsFrom.page"
      :page-sizes="[5, 10, 15, 20]"
      layout="prev, pager, next,sizes, jumper"
      :total="total">
      </el-pagination>

      <!-- 对话框 -->
      <Dialog
      :title="title"
      :form="form"
      ref="dialogRef"
      @addSubject="addSubject"
      ></Dialog>
    </el-card>
  </div>
</template>

<script>
import Dialog from './../components/subjects-add.vue'
import { update, add, list, remove, detail } from '@/api/hmmm/subjects'
export default {
  name: 'Subjects',
  components: {
    Dialog
  },
  data () {
    return {
      subjectList: [], // 学科列表数据
      subjectsFrom: {
        subjectName: '', // 学科名称
        page: 1, // 当前页数
        pagesize: 5 // 页尺寸
      },
      form: {
        subjectName: '',
        isFrontDisplay: 1
      },
      total: 0, // 总条数
      dialogVisible: false, // 对话框显示与隐藏

      rules: {
        subjectName: [
          { required: true, message: '请输入学科名称', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 1 到 10 个字符', trigger: 'blur' }
        ]
      },
      title: '',
      subId: 0 // 修改需要传递的id

    }
  },
  created () {
    this.getSubjectList()
  },
  methods: {
    // 展示对话框
    showDialog () {
      this.title = '新增'
      this.$refs.dialogRef.showDialog()
    },
    // 每页条数
    handleSizeChange (val) {
      this.subjectsFrom.page = 1 // 改完每页条数返回第一页
      this.subjectsFrom.pagesize = val
      this.getSubjectList() // 重新获取列表
    },
    // 当前页数
    handleCurrentChange (val) {
      this.subjectsFrom.page = val
      this.getSubjectList() // 重新获取列表
    },
    // 自定义索引列 使用方法自定义索引列从0开始的
    indexMethod (index) {
      // 前一页的页码 * 每页条数 + 1
      // console.log(index)
      return index + (this.subjectsFrom.page - 1) * this.subjectsFrom.pagesize + 1
    },
    // 渲染学科列表
    async getSubjectList () {
      // api目录中的函数和接口和数据相关，api函数是以 对象 方式传递
      const { data } = await list(this.subjectsFrom)
      console.log(data)
      this.total = data.counts
      this.subjectList = data.items
    },

    // 新增/修改学科
    addSubject () {
      // 1. 表单预校验
      // 使用子组件的ref
      //  this.$refs['子组件的ref对象'].$refs['子组件表单的ref对象']
      this.$refs.dialogRef.$refs.ruleForm.validate(async boo => {
        if (!boo) return
        // 判断是新增还是修改
        if (this.title === '修改') {
          // 编辑功能
          console.log('编辑')
          const name = this.form.subjectName
          console.log(name)
          await update({ id: this.subId, isFrontDisplay: this.form.isFrontDisplay, subjectName: this.form.subjectName })
        } else {
          // 新增功能
          // 发送请求添加数据
          console.log('新增')
          await add(this.form)
        }
        // 重新渲染
        this.page = 1
        this.$emit('getSubjectList')
        this.getSubjectList()
        // 清除表单
        this.form = {}
      })
      this.$refs.dialogRef.closeDialog()
    },
    // 查询回显
    async editSubject (id) {
      this.$refs.dialogRef.showDialog()
      this.title = '修改'
      this.subId = id
      const { data } = await detail({ id })
      // console.log(data.subjectName, data.isFrontDisplay)
      this.form = data
    },
    // 删除学科
    delSubject (id) {
      this.$confirm('确定要删除该学科吗?', '提示', {
        type: 'warning'
      }).then(async () => {
        await remove({ id })
        this.$message.success('删除成功!')
        // 如果数组长度只有一条了, 删除完是有bug, 需要让page--
        if (this.subjectList.length === 1 && this.subjectsFrom.page > 1) {
          this.subjectsFrom.page--
        }
        this.getSubjectList()
      }).catch(() => {
        this.$message.info('已取消删除')
      })
    },
    // 搜索
    search () {
      this.subjectsFrom.pagesize = 5
      this.getSubjectList()
    },
    // 清除
    reset () {
      this.subjectsFrom = {
        subjectName: '', // 学科名称
        page: 1, // 当前页数
        pagesize: 5 // 页尺寸
      }
      // 重新渲染
      this.getSubjectList()
    },
    // 学科分类跳转到目录
    toDirectorys (row) {
      const { id, subjectName: name } = row
      // id和name是约定好的参数，将来在目录按照这种约定取值
      this.$router.push({ path: 'directorys', query: { id, name } })
    },
    // 学科标签跳转到标签
    toTags (row) {
      const { id, subjectName: name } = row
      // id和name是约定好的参数，将来在目录按照这种约定取值
      this.$router.push({ path: 'tags', query: { id, name } })
    }
  }
}
</script>

<style scoped lang='scss'>
.container-subjects {
  padding: 10px 8px;
}
</style>
