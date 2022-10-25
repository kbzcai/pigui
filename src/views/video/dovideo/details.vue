<template>
    <el-dialog
            title="详细信息"
            append-to-body
            width="80%"
            :close-on-click-modal="false"
            v-model="visible">
        <div class="avue-crud">
            <el-table
                    :data="dataList"
                    border
                    v-loading="dataListLoading">
                <el-table-column
                        prop="vdid"
                        header-align="center"
                        align="center"
                        label="主键"
                        v-if="false">
                </el-table-column>

                <el-table-column
                        prop="vid"
                        header-align="center"
                        align="center"
                        label="关联的vid"
                        v-if="false">
                </el-table-column>
                <el-table-column
                        prop="vdType"
                        header-align="center"
                        align="center"
                        label="违法类型">
                </el-table-column>
                <el-table-column
                        prop="vdPicture"
                        header-align="center"
                        align="center"
                        label="截图"
                        v-if="false">
                </el-table-column>
                <el-table-column
                        prop="vdTime1"
                        header-align="center"
                        align="center"
                        label="违法开始时间">
                </el-table-column>
                <el-table-column
                        prop="vdTime2"
                        header-align="center"
                        align="center"
                        label="违法结束时间">
                </el-table-column>
                <el-table-column
                        prop="confirmTime"
                        header-align="center"
                        align="center"
                        label="确认时间">
                </el-table-column>
                <el-table-column
                        prop="confirmUser"
                        header-align="center"
                        align="center"
                        label="确认人">
                </el-table-column>
                <el-table-column
                        prop="vdStatus"
                        header-align="center"
                        align="center"
                        label="状态">
                    <template #="scope">
                        <span v-if="scope.row.vdStatus == 0">未确认</span>
                        <span v-if="scope.row.vdStatus == 1">通过</span>
                        <span v-if="scope.row.vdStatus == 2">未通过</span>
                    </template>
                </el-table-column>
                <el-table-column
                        prop="remark"
                        header-align="center"
                        align="center"
                        label="备注"
                        v-if="false">
                </el-table-column>
                <el-table-column
                        prop="createTime"
                        header-align="center"
                        align="center"
                        label="创建时间">
                </el-table-column>
                <el-table-column
                        header-align="center"
                        align="center"
                        label="操作">
                    <template #="scope">
                        <el-button type="text" size="small"
                                   icon="el-icon-edit" @click="addOrUpdateHandle(scope.row)">人工确认
                        </el-button>
                    </template>
                </el-table-column>
            </el-table>
        </div>

        <div class="avue-crud__pagination">
            <el-pagination
                    @size-change="sizeChangeHandle"
                    @current-change="currentChangeHandle"
                    :current-page="pageIndex"
                    :page-sizes="[10, 20, 50, 100]"
                    :page-size="pageSize"
                    :total="totalPage"
                    background
                    layout="total, sizes, prev, pager, next, jumper">
            </el-pagination>
        </div>

        <el-dialog
                width="80%"
                title="人工处理"
                v-model="innerVisible"
                @close="closeDialog()"
                append-to-body>
            <el-form :inline="true" :model="videoDetail" :rules="dataRule" ref="videoDetail"
                     @keyup.enter.native="dataFormSubmit()">
                <!--                    <el-image v-for="url in imgUrlList"-->
                <!--                              :key="url"-->
                <!--                              :src="url"-->
                <!--                              :preview-src-list="imgUrlList"-->
                <!--                              lazy>-->
                <!--                        <div slot="error" class="image-slot">-->
                <!--                            <i class="el-icon-picture-outline"></i>-->
                <!--                        </div>-->
                <!--                    </el-image>-->
                <el-carousel indicator-position="outside" height="500px">
                    <el-carousel-item v-for="url in imgUrlList" :key="url">
                        <el-image :src="url" :preview-src-list="url.split()">
                            <!--                                <div slot="error" class="image-slot">-->
                            <!--                                    <i class="el-icon-picture-outline"></i>-->
                            <!--                                </div>-->
                            <!--                            </img>-->
                        </el-image>
                    </el-carousel-item>
                </el-carousel>

                <el-form-item label="处理状态" prop="vdStatus">
                    <el-select v-model="videoDetail.vdStatus">
                        <el-option label="通过" value="1"></el-option>
                        <el-option label="未通过" value="2"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="违法行为" prop="vdType">
                    <el-select v-model="videoDetail.vdType" :disabled="videoDetail.vdStatus*1==2" :required="isHaveTo">
                        <el-option label="违法搜身" value="违法搜身"></el-option>
                        <el-option label="未依法收案" value="未依法收案"></el-option>
                        <el-option label="出警不及时" value="出警不及时"></el-option>
                        <el-option label="出警不规范" value="出警不规范"></el-option>
                        <el-option label="未依法接收证据" value="未依法接收证据"></el-option>
                        <el-option label="违法搜查" value="违法搜查"></el-option>
                        <el-option label="违法辨认" value="违法辨认"></el-option>
                        <el-option label="言行不规范" value="言行不规范"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <template #footer>
                <div class="dialog-footer">
                    <el-button @click="innerVisible = false">取消</el-button>
                    <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
                </div>
            </template>
        </el-dialog>
        <template #footer>
            <div class="dialog-footer">
                <el-button @click="visible = false">取消</el-button>
            </div>
        </template>
    </el-dialog>

</template>

<script>
    import {fetchList, delObj, fetchListByVid, putObj} from '@/api/dovideodetail'
    import {mapGetters} from 'vuex'

    export default {
        // inject: ['reload'],
        props: {
            vid: {
                type: String,
                default: ''
            }
        },
        data() {
            let validateName = (rule, value, callback) => {
                // 当活动名称为空值且为必填时，抛出错误，反之通过校验
                if (this.videoDetail.vdType === '' && this.isHaveTo) {
                    console.log("校验了")
                    callback(new Error("请选择违法行为"));
                } else {
                    console.log("没校验")
                    callback();
                }
            };
            return {
                visible: false,
                canSubmit: false,
                dataForm: {
                    key: ''
                },
                videodetail: {
                    vid: ''
                },
                videoDetail: {
                    vdid: '',
                    vdType: '',
                    vdStatus: '',
                },
                dataRule: {
                    vdStatus: [
                        {required: true, message: '请选择状态', trigger: 'blur'},
                    ],
                    vdType: [{validator: validateName}]
                },
                dataList: [],
                imgUrlList: [],
                pageIndex: 1,
                pageSize: 10,
                totalPage: 0,
                dataListLoading: false,
                addOrUpdateVisible: false,
                innerVisible: false
            }
        },
        components: {},
        created() {
            console.log(localStorage.getItem("vid") + "created")
            this.videodetail.vid = localStorage.getItem("vid")
            this.init(this.vid)
            this.getDataList()


        },
        computed: {
            ...mapGetters(['permissions']),
            isHaveTo: function () {
                console.log(this.videoDetail.vdStatus)
                if (this.videoDetail.vdStatus == '1') {
                    return true;
                    console.log("通过")
                }
                if (this.videoDetail.vdStatus == '2') {
                    this.videoDetail.vdType = ''
                    return false;
                    console.log("未通过")
                }
                return this.videoDetail.vdStatus != 2;
            }
        },
        methods: {
            dataFormSubmit() {
                this.$refs['videoDetail'].validate((valid) => {
                    if (valid) {
                        this.canSubmit = false;
                        if (this.videoDetail.vdid) {
                            putObj(this.videoDetail).then(data => {
                                this.$notify.success('处理成功')
                                this.innerVisible = false
                                this.$emit('refreshDataList')
                                this.getDataList()
                            })
                                .catch(() => {
                                    this.canSubmit = true;
                                });
                        }

                    }
                })
                this.getDataList()
            },
            // 获取数据列表
            init(id) {
                this.visible = true
                this.canSubmit = true
                console.log(id)
                this.videodetail.vid = id
            },
            getDataList() {
                console.log("获取到的vid是" + localStorage.getItem("vid"))
                this.dataListLoading = true
                fetchListByVid(Object.assign({
                    current: this.pageIndex,
                    size: this.pageSize
                }, this.videodetail)).then(response => {
                    this.dataList = response.data.data.records
                    this.totalPage = response.data.data.total
                })
                this.dataListLoading = false
            },
            // 每页数
            sizeChangeHandle(val) {
                this.pageSize = val
                this.pageIndex = 1
                this.getDataList()
            },
            // 当前页
            currentChangeHandle(val) {
                this.pageIndex = val
                this.getDataList()
            },
            // 新增 / 修改
            addOrUpdateHandle(row) {
                console.log(row.vdid + "11111111111111111111")
                this.innerVisible = true
                this.videoDetail.vdid = row.vdid;
                this.imgUrlList = row.vdPicture.split(",")
                console.log(this.imgUrlList)
                // this.addOrUpdateVisible = true
                // this.$nextTick(() => {
                //     this.$refs.addOrUpdate.init(id)
                // })
            },
            // 删除
            deleteHandle(id) {
                this.$confirm('是否确认删除ID为' + id, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(function () {
                    return delObj(id)
                }).then(data => {
                    this.$message.success('删除成功')
                    this.getDataList()
                }).catch(() => {
                })
            },
            closeDialog() {
                this.$refs["videoDetail"].resetFields()
            }
        }
    }
</script>

<style scoped>
    .el-carousel__item h3 {
        color: #475669;
        font-size: 18px;
        opacity: 0.75;
        line-height: 300px;
        margin: 0;
    }

    .el-carousel__item:nth-child(2n) {
        background-color: #99a9bf;
    }

    .el-carousel__item:nth-child(2n+1) {
        background-color: #d3dce6;
    }
</style>