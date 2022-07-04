<template>
  <div class="container-tags">
    <el-card>
      <!-- 吱~ -->
      <div slot="header" v-if="subject.id && subject.name">
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item>学科管理</el-breadcrumb-item>
          <el-breadcrumb-item>{{ subject.name }}</el-breadcrumb-item>
          <el-breadcrumb-item>目录管理</el-breadcrumb-item>
        </el-breadcrumb>
      </div>
      <!-- 筛选 -->
      <el-row>
        <!-- 表单 -->
        <el-col :span="18">
          <el-form label-width="80px" :inline="true" size="small">
            <el-form-item label="目录名称">
              <el-input
                style="200px"
                v-model="requestParams.tagName"
              ></el-input>
            </el-form-item>
            <el-form-item label="状态">
              <el-select v-model="requestParams.state">
                <el-option :value="1" label="启用"></el-option>
                <el-option :value="0" label="禁用"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item>
              <el-button @click="clear()">清除</el-button>
              <el-button type="primary" @click="Seek()">搜索</el-button>
            </el-form-item>
          </el-form>
        </el-col>

        <el-col :span="6" style="text-align:right">
          <el-button
            v-if="subject.id && subject.name"
            type="text"
            icon="el-icon-back"
            @click="$router.back()"
            >返回学科</el-button
          >
          <!-- 按钮 -->
          <el-button
            type="success"
            icon="el-icon-edit"
            size="small"
            @click="openDialog()"
            >新增目录</el-button
          >
        </el-col>
      </el-row>
      <!-- 总计 -->
      <el-alert
        :title="`数据一共${total}条`"
        type="info"
        :closable="false"
        show-icon
      ></el-alert>
      <!-- 表格 -->
      <el-table :data="tags">
        <el-table-column
          label="序号"
          type="index"
          width="80px"
        ></el-table-column>
        <el-table-column label="所属学科" prop="subjectName"></el-table-column>
        <el-table-column label="标签名称" prop="tagName"></el-table-column>
        <el-table-column label="创建者" prop="username"></el-table-column>
        <el-table-column label="创建日期">
          <template slot-scope="scope">{{
            scope.row.addDate | parseTimeByString
          }}</template>
        </el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">{{
            scope.row.state === 1 ? "已禁用" : "已启用"
          }}</template>
        </el-table-column>
        <el-table-column label="操作" width="150px">
          <template slot-scope="scope">
            <el-button type="text" @click="toggleState(scope.row)">{{
              scope.row.state === 1 ? "禁用" : "启用"
            }}</el-button>
            <el-button
              type="text"
              @click="openDialog(scope.row)"
              :disabled="scope.row.state === 1"
              >修改</el-button
            >
            <el-button
              type="text"
              @click="delTag(scope.row)"
              :disabled="scope.row.state === 1 || scope.row.totals > 0"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-pagination
        class="Paging"
        background
        layout="prev, pager, next,sizes,jumper"
        :total="total"
        :page-size="requestParams.pagesize"
        :current-page="requestParams.page"
        @current-change="pager"
        :page-sizes="[5, 10, 20, 50]"
        @size-change="handleSizeChange"
        hide-on-single-page
      >
        <!-- 需求分为5，10，20，50这种分页的切换 -->
      </el-pagination>
    </el-card>
    <!-- 添加|修改 -->
    <tags-add
      ref="tagssAdd"
      :data="currTag"
      @updateList="updateList()"
    ></tags-add>
  </div>
</template>

<script>
// 列表api
import { list, changeState, remove } from "@/api/hmmm/tags";
import TagsAdd from "../components/tags-add";
export default {
  name: "tags-page",
  components: { TagsAdd },
  data() {
    return {
      // 筛选参数
      requestParams: {
        // 标签名称关键字
        tagName: null,
        state: null,
        // 页码
        page: 1,
        // 尺寸
        pagesize: 10
      },
      // 标签列表
      tags: [],
      total: 0,
      currTag: {}
    };
  },
  created() {
    this.getList();
  },
  computed: {
    subject() {
      return this.$route.query || {};
    }
  },
  watch: {
    "$route.query": function() {
      this.getList();
    }
  },
  methods: {
    openDialog(tag = {}) {
      this.currTag = tag;
      this.$nextTick(() => {
        this.$refs.tagssAdd.open();
      });
    },
    // 切换状态
    async toggleState(tag) {
      await changeState({
        id: tag.id,
        state: tag.state === 1 ? 0 : 1
      });
      this.$message.success("操作成功");
      tag.state = tag.state === 1 ? 0 : 1;
    },
    async delTag(tag) {
      await this.$confirm("此操作将永久删除该标签, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      });
      await remove(tag);
      this.$message.success("删除成功");
      this.getList();
    },
    // 查询列表
    async getList() {
      this.requestParams.subjectID = this.subject.id || null;
      // api目录中的函数和接口相关，api函数是以对象传递
      const { data } = await list(this.requestParams);
      // 给data中的数据赋值
      this.tags = data.items;
      this.total = data.counts;
    },
    // 切换分页方法
    pager(newPage) {
      this.requestParams.page = newPage;
      this.getList();
    },
    // 切换条数
    handleSizeChange(size) {
      this.requestParams.page = 1;
      this.requestParams.pagesize = size;
      this.getList();
    },
    // 搜索
    Seek() {
      // 在此业务中，关键词是双向绑定的，如果有其它需要改动的参数那就在搜索之前改
      this.requestParams.page = 1;
      this.getList();
    },
    // 清除（把删选功能清掉）
    clear() {
      // this.requestParams={
      //    tagName: null,
      //   state: null,
      //   // 页码
      //   page: 1,
      //   // 尺寸
      //   pagesize: 10
      // }(第一种清除实现)
      this.requestParams.tagName = null;
      this.requestParams.state = null;
      this.getList();
      // 第二种只清除搜索条件一项数据
    },
    // 新增|修改 后更新列表
    updateList() {
      if (!this.currTag.id) {
        this.requestParams.page = 1;
      }
      this.getList();
    }
  }
};
</script>

<style scoped lang="scss">
.container-tags {
  padding: 10px 15px;
  .Paging {
    margin-top: 15px;
    margin-bottom: 15px;
    float: right;
  }
}
</style>
