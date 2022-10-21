<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    append-to-body
    :close-on-click-modal="false"
    @close="closeDialog()"
    v-model="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="主键" prop="vid" v-if="dataForm.id">
        <el-input v-model="dataForm.vid" placeholder="主键" disabled></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime" v-if="dataForm.id">
        <el-input v-model="dataForm.createTime" placeholder="创建时间" disabled></el-input>
    </el-form-item>
    <el-form-item label="线路来源" prop="lineSource">
        <el-input v-model="dataForm.lineSource" placeholder="线路来源"></el-input>
    </el-form-item>
    <el-form-item label="音视频名称" prop="fileName">
        <el-input v-model="dataForm.fileName" placeholder="音视频名称"></el-input>
    </el-form-item>
    <el-form-item label="音视频路径" prop="filePath">
        <el-input v-model="dataForm.filePath" placeholder="音视频路径"></el-input>
    </el-form-item>
    <el-form-item label="音视频开始日期" prop="fileDate1">
        <el-input v-model="dataForm.fileDate1" placeholder="音视频开始日期"></el-input>
    </el-form-item>
    <el-form-item label="音视频结束日期" prop="fileDate2">
        <el-input v-model="dataForm.fileDate2" placeholder="音视频结束日期"></el-input>
    </el-form-item>
    <el-form-item label="确认时间" prop="confirmTime">
        <el-input v-model="dataForm.confirmTime" placeholder="确认时间"></el-input>
    </el-form-item>
    <el-form-item label="确认人" prop="confirmUser">
        <el-input v-model="dataForm.confirmUser" placeholder="确认人"></el-input>
    </el-form-item>
    <el-form-item label="状态（0：未确认，1：确认，2：不是）" prop="fileStatus">
        <el-input v-model="dataForm.fileStatus" placeholder="状态（0：未确认，1：确认，2：不是）"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="remark">
        <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
    </el-form-item>
    </el-form>
    <template #footer>
      <div class="dialog-footer">
        <el-button @click="visible = false">取消</el-button>
        <el-button type="primary" @click="dataFormSubmit()" v-if="canSubmit">确定</el-button>
      </div>
    </template>
  </el-dialog>
</template>

<script>
    import {getObj, addObj, putObj} from '@/api/dovideo'

    export default {
    data () {
      return {
        visible: false,
        canSubmit: false,
        dataForm: {
          vid: '',
          createTime: '',
          lineSource: '',
          fileName: '',
          filePath: '',
          fileDate1: '',
          fileDate2: '',
          confirmTime: '',
          confirmUser: '',
          fileStatus: '',
          remark: '',
        },
        dataRule: {
          lineSource: [
            { required: true, message: '线路来源不能为空', trigger: 'blur' }
          ],

          fileName: [
            { required: true, message: '音视频名称不能为空', trigger: 'blur' }
          ],

          filePath: [
            { required: true, message: '音视频路径不能为空', trigger: 'blur' }
          ],

          fileDate1: [
            { required: true, message: '音视频开始日期不能为空', trigger: 'blur' }
          ],

          fileDate2: [
            { required: true, message: '音视频结束日期不能为空', trigger: 'blur' }
          ],

          confirmTime: [
            { required: true, message: '确认时间不能为空', trigger: 'blur' }
          ],

          confirmUser: [
            { required: true, message: '确认人不能为空', trigger: 'blur' }
          ],

          fileStatus: [
            { required: true, message: '状态（0：未确认，1：确认，2：不是）不能为空', trigger: 'blur' }
          ],

          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ],

        }
      }
    },
    methods: {
      init (id) {
        this.visible = true;
        this.canSubmit = true;
        this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
            if (id) {
            getObj(id).then(response => {
                this.dataForm = response.data.data
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.canSubmit = false;
            if (this.dataForm.vid) {
                putObj(this.dataForm).then(data => {
                    this.$notify.success('修改成功')
                    this.visible = false
                    this.$emit('refreshDataList')
                }).catch(() => {
                    this.canSubmit = true;
                });
            } else {
                addObj(this.dataForm).then(data => {
                    this.$notify.success('添加成功')
                    this.visible = false
                    this.$emit('refreshDataList')
                }).catch(() => {
                    this.canSubmit = true;
                });
            }
          }
        })
      },
      //重置表单
      closeDialog() {
          this.$refs["dataForm"].resetFields()
      }
    }
  }
</script>
