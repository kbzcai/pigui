<template>
  <el-dialog
    title="新增设备"
    append-to-body
    :close-on-click-modal="false"
    @close="closeDialog()"
    v-model="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
<!--    <el-form-item label="主键" prop="vlid" v-if="dataForm.id">-->
<!--        <el-input v-model="dataForm.vlid" placeholder="主键" disabled></el-input>-->
<!--    </el-form-item>-->
<!--    <el-form-item label="创建时间" prop="createTime">-->
<!--        <el-date-picker-->
<!--                v-model="dataForm.createTime"-->
<!--                type="datetime"-->
<!--                format="YYYY-MM-DD HH:mm:ss"-->
<!--                value-format="YYYY-MM-DD HH:mm:ss"-->
<!--                placeholder="选择日期时间">-->
<!--        </el-date-picker>-->
<!--    </el-form-item>-->
    <el-form-item label="标识" prop="vlKey">
        <el-input v-model="dataForm.vlKey" placeholder="视频线路标识"></el-input>
    </el-form-item>
    <el-form-item label="名称" prop="vlName">
        <el-input v-model="dataForm.vlName" placeholder="视频线路名称"></el-input>
    </el-form-item>
    <el-form-item label="所属人" prop="vlUser">
        <el-input v-model="dataForm.vlUser" placeholder="视频线路所属人"></el-input>
    </el-form-item>
    <el-form-item label="RTSP 流" prop="vlRtsp">
        <el-input v-model="dataForm.vlRtsp" placeholder="视频线路 RTSP 流"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="vlStatus">
        <el-select v-model="dataForm.vlStatus">
            <el-option label="正常" value="1"></el-option>
            <el-option label="停用" value="2"></el-option>
        </el-select>
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
    import {getObj, addObj, putObj} from '@/api/dovideoline'

    export default {
    data () {
      return {
        visible: false,
        canSubmit: false,
        dataForm: {
          vlid: '',
          createTime: '',
          vlKey: '',
          vlName: '',
          vlUser: '',
          vlRtsp: '',
          vlStatus: '',
          remark: '',
        },
        dataRule: {
          vlKey: [
            { required: true, message: '视频线路标识不能为空', trigger: 'blur' }
          ],

          vlName: [
            { required: true, message: '视频线路名称不能为空', trigger: 'blur' }
          ],

          vlUser: [
            { required: true, message: '视频线路所属人不能为空', trigger: 'blur' }
          ],

          // vlRtsp: [
          //   { required: true, message: '视频线路 RTSP 流不能为空', trigger: 'blur' }
          // ],

          vlStatus: [
            { required: true, message: '状态（1：正常；2：停用）不能为空', trigger: 'blur' }
          ],

          // remark: [
          //   { required: true, message: '备注不能为空', trigger: 'blur' }
          // ],

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
            console.log(this.dataForm.createTime)
          if (valid) {
            this.canSubmit = false;
            if (this.dataForm.vlid) {
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
