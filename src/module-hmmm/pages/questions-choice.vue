<template>
  <div class='container'>
    <el-card shadow="never">
      <!-- 搜索 -->
      <el-form size="small" label-width="80px">
        <el-row>
          <!-- 新增试题 -->
            <el-button
            type="success"
            size="small"
            icon="el-icon-edit"
            style="float:right; margin: 10px"
            @click="toNewQuestion"
            >
            新增试题
            </el-button>
        </el-row>
        <el-row>
          <el-col :span="6">
              <el-form-item label="学科">
                <el-select v-model="requestParameters.subjectID" @change="handleSecondary" style="width: 100%">
                  <el-option
                  v-for="(item,index) in subList"
                  :key="index"
                  :value="item.value"
                  :label="item.label"
                  ></el-option>
                </el-select>
              </el-form-item>
          </el-col>
          <el-col :span="6">
              <el-form-item label="二级目录">
                <el-select v-model="requestParameters.catalogID" style="width: 100%">
                  <el-option
                  v-for="(item,index) in catalogList"
                  :key="index"
                  :value="index"
                  :label="item.label"
                  ></el-option>
                </el-select>
              </el-form-item>
          </el-col>
          <el-col :span="6">
              <el-form-item label="标签">
                <el-select v-model="requestParameters.tag" style="width: 100%">
                  <el-option
                  v-for="(item,index) in subList"
                  :key="index"
                  :value="item.value"
                  :label="item.label"></el-option>
                </el-select>
              </el-form-item>
          </el-col>
          <el-col :span="6">
              <el-form-item label="关键字">
                <el-input v-model="requestParameters.keywords" type="text"></el-input>
              </el-form-item>
          </el-col>

          <el-col :span="6">
            <el-form-item label="试题类型">
              <el-select v-model="requestParameters.queType" style="width: 100%">
                <el-option
                v-for="(item,index) in questionTypeList"
                :key="index"
                :label="item.label"
                :value="item.value"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="难度">
              <el-select v-model="requestParameters.difficult" style="width: 100%">
                <el-option v-for="(item,index) in difficulty" :key="index" :label="item.label" :value="item.value"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="方向">
              <el-select v-model="requestParameters.direct" style="width: 100%">
                <el-option v-for="(item,index) in direction" :key="index" :label="item" :value="index"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="录入人">
              <el-select v-model="requestParameters.creator" style="width: 100%">
                <el-option value="1" label="超级管理员"></el-option>
                <el-option value="2" label="录入管理员"></el-option>
              </el-select>
            </el-form-item>
          </el-col>

          <el-col :span="6">
            <el-form-item label="题目备注">
              <el-input v-model="requestParameters.remark" type="text"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="企业简称">
              <el-input v-model="requestParameters.abbreviation" type="text"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="城市">
              <el-select v-model="requestParameters.province" @change="handleProvince" style="width: 48%; margin-right: 2%">
                <el-option
                  v-for="(item,index) in citySelect.province"
                  :key="index"
                  :label="item"
                  :value="item"></el-option>
              </el-select>
              <el-select v-model="requestParameters.city" style="width: 50%">
                <el-option
                  v-for="(item,index) in citySelect.cityData"
                  :key="index"
                  :label="item"
                  :value="item"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item>
            <el-button @click="handleClear">清除</el-button>
            <el-button type="primary" @click="handleFilter">搜索</el-button>
          </el-form-item>

          </el-col>
        </el-row>

      </el-form>

      <!-- 标签页 -->
      <el-tabs v-model="activeTag" type="card" @tab-click="handleClick" style="margin: 10px">
        <el-tab-pane label="全部" name="first">
          <el-table
              :data="questionChoiceList"
              border
              style="width: 100%">
              <el-table-column
                fixed
                label="试题编号"
                prop="number"
                width="150">
              </el-table-column>
              <el-table-column
                label="学科"
                prop="tags"
                width="120">
              </el-table-column>
              <el-table-column
                label="目录"
                prop="catalog"
                width="120">
              </el-table-column>
              <el-table-column
                label="题型"
                width="120">
                <template #default="{row}">
                  <span v-if="row.questionType == 1">单选 </span>
                  <span v-else-if="row.questionType == 2">多选</span>
                  <span v-else>简单</span>
                </template>
              </el-table-column>
              <el-table-column
                label="题干"
                width="300">
                <template #default="{ row }">
                  <span v-html="row.question"></span>
                </template>
              </el-table-column>
              <el-table-column
                label="录入时间"
                width="120">
                <template #default="{row}">
                  {{row.addDate | parseTimeByString}}
                </template>
              </el-table-column>
              <el-table-column
                label="难度"
                width="120">
                <template #default="{row}">
                  <span v-if="row.difficulty == 1">简单 </span>
                  <span v-else-if="row.difficulty == 2">一般</span>
                  <span v-else>困难</span>
                </template>
              </el-table-column>
              <el-table-column
                label="录入人"
                prop="creator"
                width="120">
              </el-table-column>
              <el-table-column
                label="审核状态"
                width="120">
                <template #default="{row}">
                  <span v-if="row.chkState == 1">待审核 </span>
                  <span v-else-if="row.chkState == 2">已审核</span>
                  <span v-else>已拒绝</span>
                </template>
              </el-table-column>
              <el-table-column
                label="审核意见"
                prop="chkRemarks"
                width="120">
              </el-table-column>
              <el-table-column
                label="审核人"
                prop="chkUser"
                width="120">
              </el-table-column>
              <el-table-column
                label="发布状态"
                width="120">
                <template #default="{row}">
                  <span v-if="row.publishState == 1">待发布</span>
                  <span v-else-if="row.publishState == 2">已发布</span>
                  <span v-else>已下架</span>
                </template>
              </el-table-column>
              <el-table-column
                fixed="right"
                label="操作"
                width="320px">
                <template #default="{row}">
                  <el-button type="text" size="small" @click="previewQuestion(row.id)">预览{{row.id}}</el-button>
                  <el-button type="text" size="small" @click="checkQuestion(row.id)" :disabled="chkDisabled(row)">审核</el-button>
                  <el-button type="text" size="small" @click="toEdit(row.id)" :disabled="editDisabled(row)">修改</el-button>
                  <el-button type="text" size="small" v-if="showShelves(row.publishState)" @click="takeDown(row)">上架</el-button>
                  <el-button type="text" size="small" v-else @click="takeDown(row)">下架</el-button>
                  <el-button type="text" size="small" @click="deleteQuestion(row.id)" :disabled="editDisabled(row)">删除</el-button>
                </template>
              </el-table-column>
          </el-table>
        </el-tab-pane>
        <el-tab-pane label="待审核" name="second">
          <el-table
              :data="questionChoiceList"
              border
              style="width: 100%">
              <el-table-column
                fixed
                label="试题编号"
                prop="number"
                width="150">
              </el-table-column>
              <el-table-column
                label="学科"
                prop="tags"
                width="120">
              </el-table-column>
              <el-table-column
                label="目录"
                prop="catalog"
                width="120">
              </el-table-column>
              <el-table-column
                label="题型"
                width="120">
                <template #default="{row}">
                  <span v-if="row.questionType == 1">单选 </span>
                  <span v-else-if="row.questionType == 2">多选</span>
                  <span v-else>简单</span>
                </template>
              </el-table-column>
              <el-table-column
                label="题干"
                width="300">
                <template #default="{ row }">
                  <span v-html="row.question"></span>
                </template>
              </el-table-column>
              <el-table-column
                label="录入时间"
                width="120">
                <template #default="{row}">
                  {{row.addDate | parseTimeByString}}
                </template>
              </el-table-column>
              <el-table-column
                label="难度"
                width="120">
                <template #default="{row}">
                  <span v-if="row.difficulty == 1">简单 </span>
                  <span v-else-if="row.difficulty == 2">一般</span>
                  <span v-else>困难</span>
                </template>
              </el-table-column>
              <el-table-column
                label="录入人"
                prop="creator"
                width="120">
              </el-table-column>
              <el-table-column
                label="审核状态"
                width="120">
                <template #default="{row}">
                  <span v-if="row.chkState == 1">待审核 </span>
                  <span v-else-if="row.chkState == 2">已审核</span>
                  <span v-else>已拒绝</span>
                </template>
              </el-table-column>
              <el-table-column
                label="审核意见"
                prop="chkRemarks"
                width="120">
              </el-table-column>
              <el-table-column
                label="审核人"
                prop="chkUser"
                width="120">
              </el-table-column>
              <el-table-column
                label="发布状态"
                width="120">
                <template #default="{row}">
                  <span v-if="row.publishState == 1">待发布</span>
                  <span v-else-if="row.publishState == 2">已发布</span>
                  <span v-else>已下架</span>
                </template>
              </el-table-column>
              <el-table-column
                fixed="right"
                label="操作"
                width="320px">
                <template #default="{row}">
                  <el-button type="text" size="small" @click="previewQuestion(row.id)">预览{{row.id}}</el-button>
                  <el-button type="text" size="small" @click="checkQuestion(row.id)" :disabled="chkDisabled(row)">审核</el-button>
                  <el-button type="text" size="small" @click="toEdit(row.id)" :disabled="editDisabled(row)">修改</el-button>
                  <el-button type="text" size="small" v-if="showShelves(row.publishState)" @click="takeDown(row)">上架</el-button>
                  <el-button type="text" size="small" v-else @click="takeDown(row)">下架</el-button>
                  <el-button type="text" size="small" @click="deleteQuestion(row.id)" :disabled="editDisabled(row)">删除</el-button>
                </template>
              </el-table-column>
          </el-table>
        </el-tab-pane>
        <el-tab-pane label="已审核" name="third">
          <el-table
              :data="questionChoiceList"
              border
              style="width: 100%">
              <el-table-column
                fixed
                label="试题编号"
                prop="number"
                width="150">
              </el-table-column>
              <el-table-column
                label="学科"
                prop="tags"
                width="120">
              </el-table-column>
              <el-table-column
                label="目录"
                prop="catalog"
                width="120">
              </el-table-column>
              <el-table-column
                label="题型"
                width="120">
                <template #default="{row}">
                  <span v-if="row.questionType == 1">单选 </span>
                  <span v-else-if="row.questionType == 2">多选</span>
                  <span v-else>简单</span>
                </template>
              </el-table-column>
              <el-table-column
                label="题干"
                width="300">
                <template #default="{ row }">
                  <span v-html="row.question"></span>
                </template>
              </el-table-column>
              <el-table-column
                label="录入时间"
                width="120">
                <template #default="{row}">
                  {{row.addDate | parseTimeByString}}
                </template>
              </el-table-column>
              <el-table-column
                label="难度"
                width="120">
                <template #default="{row}">
                  <span v-if="row.difficulty == 1">简单 </span>
                  <span v-else-if="row.difficulty == 2">一般</span>
                  <span v-else>困难</span>
                </template>
              </el-table-column>
              <el-table-column
                label="录入人"
                prop="creator"
                width="120">
              </el-table-column>
              <el-table-column
                label="审核状态"
                width="120">
                <template #default="{row}">
                  <span v-if="row.chkState == 1">待审核 </span>
                  <span v-else-if="row.chkState == 2">已审核</span>
                  <span v-else>已拒绝</span>
                </template>
              </el-table-column>
              <el-table-column
                label="审核意见"
                prop="chkRemarks"
                width="120">
              </el-table-column>
              <el-table-column
                label="审核人"
                prop="chkUser"
                width="120">
              </el-table-column>
              <el-table-column
                label="发布状态"
                width="120">
                <template #default="{row}">
                  <span v-if="row.publishState == 1">待发布</span>
                  <span v-else-if="row.publishState == 2">已发布</span>
                  <span v-else>已下架</span>
                </template>
              </el-table-column>
              <el-table-column
                fixed="right"
                label="操作"
                width="320px">
                <template #default="{row}">
                  <el-button type="text" size="small" @click="previewQuestion(row.id)">预览{{row.id}}</el-button>
                  <el-button type="text" size="small" @click="checkQuestion(row.id)" :disabled="chkDisabled(row)">审核</el-button>
                  <el-button type="text" size="small" @click="toEdit(row.id)" :disabled="editDisabled(row)">修改</el-button>
                  <el-button type="text" size="small" v-if="showShelves(row.publishState)" @click="takeDown(row)">上架</el-button>
                  <el-button type="text" size="small" v-else @click="takeDown(row)">下架</el-button>
                  <el-button type="text" size="small" @click="deleteQuestion(row.id)" :disabled="editDisabled(row)">删除</el-button>
                </template>
              </el-table-column>
          </el-table>
        </el-tab-pane>
        <el-tab-pane label="已拒绝" name="fourth">
          <el-table
              :data="questionChoiceList"
              border
              style="width: 100%">
              <el-table-column
                fixed
                label="试题编号"
                prop="number"
                width="150">
              </el-table-column>
              <el-table-column
                label="学科"
                prop="tags"
                width="120">
              </el-table-column>
              <el-table-column
                label="目录"
                prop="catalog"
                width="120">
              </el-table-column>
              <el-table-column
                label="题型"
                width="120">
                <template #default="{row}">
                  <span v-if="row.questionType == 1">单选 </span>
                  <span v-else-if="row.questionType == 2">多选</span>
                  <span v-else>简单</span>
                </template>
              </el-table-column>
              <el-table-column
                label="题干"
                width="300">
                <template #default="{ row }">
                  <span v-html="row.question"></span>
                </template>
              </el-table-column>
              <el-table-column
                label="录入时间"
                width="120">
                <template #default="{row}">
                  {{row.addDate | parseTimeByString}}
                </template>
              </el-table-column>
              <el-table-column
                label="难度"
                width="120">
                <template #default="{row}">
                  <span v-if="row.difficulty == 1">简单 </span>
                  <span v-else-if="row.difficulty == 2">一般</span>
                  <span v-else>困难</span>
                </template>
              </el-table-column>
              <el-table-column
                label="录入人"
                prop="creator"
                width="120">
              </el-table-column>
              <el-table-column
                label="审核状态"
                width="120">
                <template #default="{row}">
                  <span v-if="row.chkState == 1">待审核 </span>
                  <span v-else-if="row.chkState == 2">已审核</span>
                  <span v-else>已拒绝</span>
                </template>
              </el-table-column>
              <el-table-column
                label="审核意见"
                prop="chkRemarks"
                width="120">
              </el-table-column>
              <el-table-column
                label="审核人"
                prop="chkUser"
                width="120">
              </el-table-column>
              <el-table-column
                label="发布状态"
                width="120">
                <template #default="{row}">
                  <span v-if="row.publishState == 1">待发布</span>
                  <span v-else-if="row.publishState == 2">已发布</span>
                  <span v-else>已下架</span>
                </template>
              </el-table-column>
              <el-table-column
                fixed="right"
                label="操作"
                width="320px">
                <template #default="{row}">
                  <el-button type="text" size="small" @click="previewQuestion(row.id)">预览{{row.id}}</el-button>
                  <el-button type="text" size="small" @click="checkQuestion(row.id)" :disabled="chkDisabled(row)">审核</el-button>
                  <el-button type="text" size="small" @click="toEdit(row.id)" :disabled="editDisabled(row)">修改</el-button>
                  <el-button type="text" size="small" v-if="showShelves(row.publishState)" @click="takeDown(row)">上架</el-button>
                  <el-button type="text" size="small" v-else @click="takeDown(row)">下架</el-button>
                  <el-button type="text" size="small" @click="deleteQuestion(row.id)" :disabled="editDisabled(row)">删除</el-button>
                </template>
              </el-table-column>
          </el-table>
        </el-tab-pane>
      </el-tabs>

      <!-- 对话框组件 -->
      <Dialog
      :form="form"
      ref="dialogRef">
      </Dialog>

      <!-- 题目审核 对话框 -->
      <CheckDialog :checkForm="checkForm" ref="checkDialogRef" @addChk="addChk"></CheckDialog>

        <!-- 分页 -->
      <el-pagination
        style="float:right; margin: 10px"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        background
        :page-size="requestParameters.pagesize"
        :current-page="requestParameters.page"
        :page-sizes="[5, 10, 15, 20]"
        layout="prev, pager, next,sizes, jumper"
        :total="total">
      </el-pagination>
        <!-- end -->
        <!-- 新增标签弹层 -->

      </el-card>
  </div>
</template>

<script>
import { simple } from '@/api/hmmm/subjects.js'
import { direction, questionType as questionTypeList, difficulty } from './../../api/hmmm/constants'
import { choice, detail, choiceCheck, remove, choicePublish } from '../../api/hmmm/questions.js'
import { provinces, citys } from '@/api/hmmm/citys.js'
import Dialog from './../components/questions-preview.vue'
import CheckDialog from './../components/questions-check.vue'
import { simple as directorySimple } from '@/api/hmmm/directorys.js'
import { simple as tagSimple } from '@/api/hmmm/tags.js'

// // 二级目录
// const twoLevelDirectory = pname => {
//   for (const item of this.subList) {
//     if (item.subjectName === pname) {
//       return item.twoLevelDirectory
//     }
//   }
//   return []
// }

export default {
  name: 'QuestionChoice',
  components: {
    Dialog,
    CheckDialog
  },
  data () {
    return {
      form: {

      },
      subList: [], // 简单学科列表
      queList: [],
      questionChoiceList: [], // 精选题库列表
      difficulty: [], // 难度
      direction: [], // 方向
      questionTypeList: [], // 试卷类型
      activeTag: 'first',
      value: '',
      creator: '', // 录入人
      requestParameters: { // 表单关联数据
        subjectID: '', // 学科
        catalogID: '', // 二级目录
        tag: '', // 标签
        keywords: '', // 关键字
        queType: '', // 试题类型
        difficult: '', // 难度
        direct: '', // 方向
        creator: '', // 录入人
        remark: '', // 备注
        abbreviation: '', // 企业简称
        province: '', // 省
        city: '', // 市
        page: 1,
        pagesize: 10
      },
      citySelect: { // 市 区 列表
        province: [],
        cityData: []
      },
      checkForm: {
        chkState: 1,
        chkRemarks: '' // 审核意见
      },
      checkId: '', // 审核id
      catalogList: [], // 二级目录列表
      total: 0
    }
  },
  created () {
    this.direction = direction
    this.questionTypeList = questionTypeList
    this.difficulty = difficulty
    this.getSubjectList()
    this.getQuestionChoiceList()
    this.getCityData()
  },
  methods: {
    handleClick (tab, event) {
      // const res = chkType.find(item => item.label === tab.label)
      if (tab.label === '全部') {
        this.getQuestionChoiceList()
      }
      if (tab.label === '待审核') {
        this.getQuestionChoiceList(1)
      }
      if (tab.label === '已审核') {
        this.getQuestionChoiceList(2)
      }
      if (tab.label === '已拒绝') {
        this.getQuestionChoiceList(0)
      }
    },
    // 跳转到试题录入
    toNewQuestion () {
      this.$router.push({ path: 'new' })
    },
    // 跳转到试题修改
    toEdit (id) {
      this.$router.push({ path: 'new', query: { id } })
    },
    // 展示对话框
    showDialog () {
      this.$refs.dialogRef.showDialog()
    },
    // 审核禁用
    chkDisabled (row) {
      if (row.chkState === 1) {
        return false
      } else {
        return true
      }
    },
    // 修改删除禁用
    editDisabled (row) {
      if (row.publishState === 1) {
        return false
      } else {
        return true
      }
    },
    // 展示上架还是下架
    // 展示上架还是下架按钮
    showShelves (state) {
      return state === 0
    },
    // 查询回显
    async previewQuestion (id) {
      console.log(id)
      this.$refs.dialogRef.showDialog()
      const { data } = await detail({ id })
      console.log(data)
      this.form = data
    },
    // 题目审核
    checkQuestion (id) {
      this.$refs.checkDialogRef.showCheckDialog()
      console.log(id)
      this.checkId = id
    },
    // 添加审核
    async addChk () {
      this.$refs.checkDialogRef.$refs.ruleForm.validate(async boo => {
        await choiceCheck({ id: this.checkId, chkState: this.checkForm.chkState, chkRemarks: this.checkForm.chkRemarks })
        // 重新渲染
        this.getQuestionChoiceList()
        this.$refs.checkDialogRef.closeDialog()
      })
    },
    // 上架下架
    async takeDown (row) {
      this.$confirm(`确定${row.publishState === 1 ? '下架' : '上架'}?`, '提示', {
        type: 'warning'
      }).then(async () => {
        row.publishState === 0 ? row.publishState = 1 : row.publishState = 0
        await choicePublish(row)
        this.$message.success('成功!')
        this.getQuestionChoiceList()
      }).catch(() => {
        this.$message.info('已取消')
      })
    },
    // 删除
    deleteQuestion (id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        type: 'warning'
      }).then(async () => {
        await remove({ id })
        this.$message.success('删除成功!')
        this.getQuestionChoiceList()
      }).catch(() => {
        this.$message.info('已取消删除')
      })
    },
    // 每页显示信息条数
    handleSizeChange (val) {
      this.requestParameters.pagesize = val
      if (this.requestParameters.page === 1) {
        this.getQuestionChoiceList(this.requestParameters)
      }
    },
    // 进入某一页
    handleCurrentChange (val) {
      this.requestParameters.page = val
      this.getQuestionChoiceList()
    },
    // 重置
    handleClear () {
      this.requestParameters = {}
      this.getQuestionChoiceList()
    },
    // 搜索信息
    handleFilter () {
      // this.requestParameters.page = 1
      // this.requestParameters.pagesize = 5
      this.getQuestionChoiceList(this.requestParameters)
    },
    // 获取简单学科列表
    async getSubjectList () {
      const res = await simple()
      console.log(res.data)
      this.subList = res.data
    },
    // 获取二级目录
    async handleSecondary () {
      // 二级目录
      const res = await directorySimple({
        subjectID: this.requestParameters.subjectID
      })
      this.catalogList = res.data
      // 标签
      const resTags = await tagSimple({ subjectID: this.requestParameters.subjectID })

      this.subList = resTags.data
    },
    // 获取精选题库列表
    async getQuestionChoiceList (chkState) {
      const { data } = await choice({ chkState })
      console.log(data.items)
      this.questionChoiceList = data.items
      this.total = data.counts
    },
    // 获取省
    getCityData () {
      this.citySelect.province = provinces()
    },
    // 选省获取到市
    handleProvince (e) {
      this.citySelect.cityData = citys(e)
      this.requestParameters.city = this.citySelect.cityData[0]
    }

  }

}
</script>

<style scoped lang='scss'></style>
