<template>
  <div class="mod-maillog">
    <el-form :inline="true" :model="searchForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="searchForm.sender" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('sys:maillog:config')" type="primary" @click="configHandle()">邮箱配置
        </el-button>
        <el-button v-if="isAuth('sys:maillog:delete')" type="danger" @click="deleteHandle()"
                   :disabled="dataListSelections.length <= 0">批量删除
        </el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="sender"
        header-align="center"
        align="center"
        label="发送人">
      </el-table-column>
      <el-table-column
        show-overflow-tooltip
        prop="receiver"
        header-align="center"
        align="center"
        label="接收人">
      </el-table-column>
      <el-table-column
        prop="subject"
        header-align="center"
        align="center"
        label="邮件主题">
      </el-table-column>
      <el-table-column
        show-overflow-tooltip
        prop="content"
        header-align="center"
        align="center"
        label="发送内容">
      </el-table-column>
      <el-table-column
        prop="sendDate"
        header-align="center"
        align="center"
        label="发送时间">
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        label="发送类型">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.type === 0" size="small">系统发送邮件</el-tag>
          <el-tag v-else-if="scope.row.type === 1" size="small" type="success">用户发送邮件</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="sendResult"
        header-align="center"
        align="center"
        label="发送结果">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.sendResult === 1" size="small" type="danger">发送失败</el-tag>
          <el-tag v-else-if="scope.row.sendResult === 0" size="small" type="success">发送成功</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button v-if="isAuth('sys:maillog:info')" type="text" size="small"
                     @click="lookHandle(scope.row.id)">查看
          </el-button>
          <el-button v-if="isAuth('sys:maillog:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 查看 -->
    <detail v-if="detailVisible" ref="look" @refreshDataList="getDataList"></detail>
    <!-- 邮箱配置 -->
    <config v-if="configVisible" ref="config"></config>
  </div>
</template>

<script>
  import Detail from './maillog-detail'
  import Config from './maillog-config'

  export default {
    data () {
      return {
        searchForm: {
          sender: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListSelections: [],
        detailVisible: false,
        configVisible: false
      }
    },
    components: {
      Detail, Config
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.$http({
          url: '/sys/maillog/list',
          method: 'get',
          params: {
            'page': this.pageIndex,
            'limit': this.pageSize,
            'sender': this.searchForm.sender
          }
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.data.records
            this.totalPage = data.data.total
          } else {
            this.dataList = []
            this.totalPage = 0
          }
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      lookHandle (id) {
        this.detailVisible = true
        this.$nextTick(() => {
          this.$refs.look.init(id)
        })
      },
      configHandle () {
        this.configVisible = true
        this.$nextTick(() => {
          this.$refs.config.init()
        })
      },
      // 删除
      deleteHandle (id) {
        let ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[删除]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: '/sys/maillog/delete',
            method: 'post',
            data: ids
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500
              })
              this.getDataList()
            }
          })
        }).catch(() => {
        })
      }
    }
  }
</script>
