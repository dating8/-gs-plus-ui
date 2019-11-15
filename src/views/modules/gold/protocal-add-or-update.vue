<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="80px">
      <el-form-item label="协议名称" prop="title">
        <el-input v-model="dataForm.title"></el-input>
      </el-form-item>
      
      <el-form-item label="备注" prop="memo">
				<el-input type="textarea" v-model="dataForm.memo" placeholder="备注"></el-input>
      </el-form-item>
			<el-form-item label="协议内容">
				<ueditor v-model="dataForm.content"></ueditor>
			</el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button v-if="!disabled" type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        disabled: false,
        visible: false,
        dataForm: {
          id: 0,
          title: '',
          memo: '',
          content: ''
        },
        dataRule: {
          title: [
            {required: true, message: '协议名称不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    methods: {
      init (id, disabled) {
				// protocalId
        this.disabled = disabled
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: `/gold/protocal/info/${this.dataForm.id}`,
              method: 'get'
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.data.title
                this.dataForm.memo = data.data.memo
                this.dataForm.content = data.data.content
								this.dataForm.avaliable = data.data.avaliable
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
              url: `/gold/protocal/${!this.dataForm.id ? 'save' : 'update'}`,
              method: !this.dataForm.id ? 'post' : 'put',
              data: {
								'avaliable':!this.dataForm.id ? '1': this.dataForm.avaliable,
                'protocalId': this.dataForm.id || undefined,
                'title': this.dataForm.title,
                'memo': this.dataForm.memo,
                'content': this.dataForm.content
              }
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500
                })
                this.visible = false
                this.$emit('refreshDataList')
              }
            })
          }
        })
      }
    }
  }
</script>
