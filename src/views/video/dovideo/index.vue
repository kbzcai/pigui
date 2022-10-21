<template>
    <div class="mod-config">
        <basic-container>
            <el-form :inline="true" :model="videoDetail">
                <el-form-item label="违法行为">
                    <el-select v-model="videoDetail.vdType" multiple collapse-tags>
                        <!--                        <el-option label="全部" value=""></el-option>-->
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
                <el-form-item label="状态">
                    <el-select v-model="videoDetail.vdStatus">
                        <el-option v-for="dict in dicData"
                                   :key="dict.value"
                                   :label="dict.label"
                                   :value="dict.value"
                        ></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="视频名称">
                    <el-input v-model="videoDetail.fileName" placeholder="请输入视频名称"></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="onSubmit" icon="el-icon-search">检索</el-button>
                </el-form-item>
            </el-form>
            <!--      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">-->
            <!--        <el-form-item>-->
            <!--          <el-button v-if="permissions.video_dovideo_add" icon="el-icon-plus" type="primary" @click="addOrUpdateHandle()">新增</el-button>-->
            <!--        </el-form-item>-->
            <!--      </el-form>-->

            <div class="avue-crud">
                <el-table
                        :data="dataList"
                        border
                        v-loading="dataListLoading">
                    <el-table-column
                            prop="vid"
                            header-align="center"
                            align="center"
                            label="主键"
                            v-if="false"
                    >
                    </el-table-column>
                    <el-table-column
                            prop="fileName"
                            header-align="center"
                            align="center"
                            label="视频名称">
                    </el-table-column>
                    <el-table-column
                            prop="filePath"
                            header-align="center"
                            align="center"
                            label="视频路径">
                    </el-table-column>
                    <el-table-column
                            prop="lineSource"
                            header-align="center"
                            align="center"
                            label="线路来源">
                    </el-table-column>
                    <!--                    <el-table-column-->
                    <!--                            prop="fileDate1"-->
                    <!--                            header-align="center"-->
                    <!--                            align="center"-->
                    <!--                            label="音视频开始日期">-->
                    <!--                    </el-table-column>-->
                    <!--                    <el-table-column-->
                    <!--                            prop="fileDate2"-->
                    <!--                            header-align="center"-->
                    <!--                            align="center"-->
                    <!--                            label="音视频结束日期">-->
                    <!--                    </el-table-column>-->
                    <el-table-column
                            prop="eventTime"
                            header-align="center"
                            align="center"
                            label="事件日期">
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
                            prop="isweifa"
                            header-align="center"
                            align="center"
                            label="是否违法行为">
                        是
                    </el-table-column>
                    <el-table-column
                            prop="fileStatus"
                            header-align="center"
                            align="center"
                            label="处理状态">
                        <template #="scope">
                            <span v-if="scope.row.fileStatus == 0">未确认</span>
                            <span v-if="scope.row.fileStatus == 1">通过</span>
                            <span v-if="scope.row.fileStatus == 2">未通过</span>
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
                            label="创建时间"
                            sortable>
                    </el-table-column>
                    <el-table-column
                            header-align="center"
                            align="center"
                            label="操作">
                        <template #="scope">
                            <el-button type="text" size="small"
                                       icon="el-icon-view" @click="getDetailHandle(scope.row.vid)">查看详情
                            </el-button>
                            <!--                            <el-button v-if="permissions.video_dovideo_edit" type="text" size="small"-->
                            <!--                                       icon="el-icon-edit" @click="addOrUpdateHandle(scope.row.vid)">修改-->
                            <!--                            </el-button>-->
                            <!--                            <el-button v-if="permissions.video_dovideo_del" type="text" size="small"-->
                            <!--                                       icon="el-icon-delete" @click="deleteHandle(scope.row.vid)">删除-->
                            <!--                            </el-button>-->
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

            <!-- 弹窗, 新增 / 修改 -->
            <table-form v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></table-form>
            <DetailTable v-if="detailVisible" ref="getDetail" :vid="vid" @refreshDataList="getDataList"></DetailTable>
        </basic-container>
    </div>
</template>

<script>
    import {fetchList, delObj} from '@/api/dovideo'
    import TableForm from './dovideo-form.vue'
    import DetailTable from './details.vue'
    import {mapGetters} from 'vuex'

    export default {
        data() {
            return {
                dataForm: {
                    key: ''
                },
                videoDetail: {
                    fileName: '',
                    vdType: '',
                    vdStatus: ''
                },
                dicData: [{
                    label: '全部',
                    value: ''
                }, {
                    label: '未确认',
                    value: '0'
                }, {
                    label: '通过',
                    value: '1'
                }, {
                    label: '未通过',
                    value: '2'
                }],
                videodetail: {
                    vid: ''
                },
                vid: '',
                dataList: [],
                pageIndex: 1,
                pageSize: 10,
                totalPage: 0,
                dataListLoading: false,
                addOrUpdateVisible: false,
                detailVisible: false
            }
        },
        components: {
            TableForm, DetailTable
        },
        created() {
            this.getDataList()
        },
        computed: {
            ...mapGetters(['permissions'])
        },
        methods: {
            onSubmit() {
                console.log(this.videoDetail.vdType)
                this.dataListLoading = true
                fetchList(Object.assign({
                    current: this.pageIndex,
                    size: this.pageSize,
                }, this.videoDetail)).then(response => {
                    this.dataList = response.data.data.records
                    this.totalPage = response.data.data.total
                })
                this.dataListLoading = false
            },
            // 获取数据列表
            getDataList() {
                this.dataListLoading = true
                fetchList(Object.assign({
                    current: this.pageIndex,
                    size: this.pageSize
                }, this.videoDetail)).then(response => {
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
            addOrUpdateHandle(id) {
                this.addOrUpdateVisible = true
                this.$nextTick(() => {
                    this.$refs.addOrUpdate.init(id)
                })
            },
            getDetailHandle(id) {
                this.detailVisible = false
                this.vid = id;
                this.$nextTick(() => {
                    this.detailVisible = true
                    // this.$refs.getDetail.init(id)
                })
                localStorage.setItem("vid", id);
                console.log("刚刚设置好" + localStorage.getItem("vid"))
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
            }
        }
    }
</script>
