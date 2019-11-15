<template>
	<div class="mod-menu">
	  <el-form :inline="true" :model="searchForm">
	    <el-form-item>
	      <el-button v-if="isAuth('sys:menu:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
	    </el-form-item>
	  </el-form>
	  <el-table
	    :data="dataList"
	    border
	    row-key="orgNo"
	    style="width: 100%;">
	    <el-table-column
	      width="200"
	      prop="title"
	      header-align="center"
	      align="center"
	      label="协议名称">
	    </el-table-column>
			<el-table-column
			  width="200"
			  prop="createTime"
			  header-align="center"
			  align="center"
			  label="添加时间">
			</el-table-column>
	    <el-table-column
	      prop="avaliable"
	      header-align="center"
	      align="center"
	      label="状态">
	      <template slot-scope="scope">
					<el-switch
					  v-model="scope.row.avaliable"
					  active-value="1"
					  inactive-value="0" @change="handleChange($event,scope.row.protocalId)">
					</el-switch>
	        <!-- <el-tag v-if="scope.row.avaliable === 0" size="small" type="danger">禁用</el-tag>
	        <el-tag v-else-if="scope.row.avaliable === 1" size="small" type="success">启用</el-tag> -->
	      </template>
	    </el-table-column>
	    <el-table-column
	      prop="createUserId"
	      header-align="center"
	      align="center"
	      label="管理员">
	      <template slot-scope="scope">
	        <span>{{transUser(scope.row.createUserId)}}</span>
	      </template>
	    </el-table-column>
	    <el-table-column
	      width="155"
	      prop="memo"
	      header-align="center"
	      align="center"
	      label="备注">
	    </el-table-column>
	    <el-table-column
	      fixed="right"
	      header-align="center"
	      align="center"
	      width="150"
	      label="操作">
	      <template slot-scope="scope">
	        <el-button v-if="isAuth('gold:protocal:update')" type="text" size="small"
	          @click="addOrUpdateHandle(scope.row.protocalId)">
	          修改
	        </el-button>
	      </template>
	    </el-table-column>
	  </el-table>
	  <!-- 弹窗, 新增 / 修改 -->
	  <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
	</div>
</template>

<script>
	import AddOrUpdate from './protocal-add-or-update'
	export default {
	  data () {
	    return {
	      searchForm: {},
	      dataList: [],
	      addOrUpdateVisible: false
	    }
	  },
	  components: {AddOrUpdate},
	  activated () {
	    this.getDataList()
	  },
	  methods: {
	    // 获取数据列表
	    getDataList () {
	      this.$http({
	        url: '/gold/protocal/queryAll',
	        method: 'get'
	      }).then(({data}) => {
	        if (data && data.code === 0) {
	          this.dataList = data.data
	        } else {
	          this.dataList = []
	        }
	      })
	    },
	    // 新增 / 修改
	    addOrUpdateHandle (id) {
	      this.addOrUpdateVisible = true
	      this.$nextTick(() => {
	        this.$refs.addOrUpdate.init(id)
	      })
	    },
			//修改状态
			handleChange(val,id){
				this.$http({
				  url: '/gold/protocal/update',
				  method: 'put',
					data: {
						'avaliable': val,
					  'protocalId': id
					}
				}).then(({data}) => {
				  this.$message({
				    message: '操作成功',
				    type: 'success',
				    duration: 1500
				  })
				})
			}
		}
	}
</script>

<style>
</style>
