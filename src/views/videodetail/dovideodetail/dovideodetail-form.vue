<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    append-to-body
    :close-on-click-modal="false"
    @close="closeDialog()"
    v-model="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="主键" prop="vdid" v-if="dataForm.id">
        <el-input v-model="dataForm.vdid" placeholder="主键" disabled></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime" v-if="dataForm.id">
        <el-input v-model="dataForm.createTime" placeholder="创建时间" disabled></el-input>
    </el-form-item>
    <el-form-item label="关联 do_video.vid" prop="vid">
        <el-input v-model="dataForm.vid" placeholder="关联 do_video.vid"></el-input>
    </el-form-item>
    <el-form-item label="违法类型" prop="vdType">
        <el-input v-model="dataForm.vdType" placeholder="违法类型"></el-input>
    </el-form-item>
    <el-form-item label="截图，多个逗号分割" prop="vdPicture">
        <el-input v-model="dataForm.vdPicture" placeholder="截图，多个逗号分割"></el-input>
    </el-form-item>
    <el-form-item label="违法开始时间" prop="vdTime1">
        <el-input v-model="dataForm.vdTime1" placeholder="违法开始时间"></el-input>
    </el-form-item>
    <el-form-item label="违法结束时间" prop="vdTime2">
        <el-input v-model="dataForm.vdTime2" placeholder="违法结束时间"></el-input>
    </el-form-item>
    <el-form-item label="确认时间（注意回填主表）" prop="confirmTime">
        <el-input v-model="dataForm.confirmTime" placeholder="确认时间（注意回填主表）"></el-input>
    </el-form-item>
    <el-form-item label="确认人" prop="confirmUser">
        <el-input v-model="dataForm.confirmUser" placeholder="确认人"></el-input>
    </el-form-item>
    <el-form-item label="状态（0：未确认，1：确认，2：不是）" prop="vdStatus">
        <el-input v-model="dataForm.vdStatus" placeholder="状态（0：未确认，1：确认，2：不是）"></el-input>
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
    import {getObj, addObj, putObj} from '@/api/dovideodetail'

    export default {
    data () {
      return {
        visible: false,
        canSubmit: false,
        dataForm: {
          vdid: '',
          createTime: '',
          vid: '',
          vdType: '',
          vdPicture: '',
          vdTime1: '',
          vdTime2: '',
          confirmTime: '',
          confirmUser: '',
          vdStatus: '',
          remark: '',
        },
        dataRule: {
          vid: [
            { required: true, message: '关联 do_video.vid不能为空', trigger: 'blur' }
          ],

          vdType: [
            { required: true, message: '违法类型不能为空', trigger: 'blur' }
          ],

          vdPicture: [
            { required: true, message: '截图，多个逗号分割不能为空', trigger: 'blur' }
          ],

          vdTime1: [
            { required: true, message: '违法开始时间不能为空', trigger: 'blur' }
          ],

          vdTime2: [
            { required: true, message: '违法结束时间不能为空', trigger: 'blur' }
          ],

          confirmTime: [
            { required: true, message: '确认时间（注意回填主表）不能为空', trigger: 'blur' }
          ],

          confirmUser: [
            { required: true, message: '确认人不能为空', trigger: 'blur' }
          ],

          vdStatus: [
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
            if (this.dataForm.vdid) {
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
