<template>
  <div class='container'>
    <el-dialog
      title="题目预览"
      :visible="dialogVisible"
      width="60%"
      @close="closeDialog">
      <el-form :model="form" ref="ruleForm" label-width="100px">
        【题型】：
          <span v-if="form.questionType == 1">单选 </span>
          <span v-else-if="form.questionType == 2">多选</span>
          <span v-else>简单</span>
          【编号】：{{form.id}}
          【难度】：
          <span v-if="form.difficulty == 1">简单 </span>
          <span v-else-if="form.difficulty == 2">一般</span>
          <span v-else>复杂</span>
          【标签】：{{form.tags}}
          【学科】：{{form.subjectName}}
          【目录】：{{form.directoryName}}
          【方向】：{{form.direction}}
        <hr>
        【题干】：<span v-html="form.question"></span>
        <div>
          <span v-if="form.questionType == 1">单选</span>
          <span v-else-if="form.questionType == 2">多选</span>
          <span v-else>简单</span>题 选项：（以下选中的选项为正确答案）

        </div>
        <hr>
        【参考答案】：<el-button type="danger" @click="preivewVideo">视频答案预览</el-button>
        <div class="video" v-show="playVideo">
          <video ref="video" :src="form.videoURL" controls style="width: 680px; height:382px"></video>
        </div>
        <hr>
        【答案解析】：<span v-html="form.question"></span>
        <hr>
        【题目备注】：{{form.remarks}}
      </el-form>
        <el-button @click="closeDialog">取 消</el-button>
        <el-button type="primary" @click="closeDialog">确 定</el-button>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'PreviewQuestion',
  props: {
    form: {
      type: Object,
      required: true
    }

  },
  data () {
    return {
      dialogVisible: false,
      playVideo: false
    }
  },
  methods: {
    // 弹出对话框
    showDialog () {
      this.dialogVisible = true
      this.$emit('showDialog')
    },
    // 打开视频
    preivewVideo () {
      this.playVideo = true
      this.$refs.video.pause()
    },
    // 关闭视频
    closeDialog () {
      this.playVideo = false
      this.$refs.video.pause()
      this.dialogVisible = false
    }

  }
}
</script>

<style scoped lang='less'></style>
