<template>
    <div class="mod-config">
        <basic-container>
            <el-form :inline="true" :model="videoline">
                <!--                <el-form-item label="状态">-->
                <!--                    <el-select v-model="videoline.vlStatus">-->
                <!--                        &lt;!&ndash;                        <el-option label="全部" value="0"></el-option>&ndash;&gt;-->
                <!--                        &lt;!&ndash;                        <el-option label="正常" value="1"></el-option>&ndash;&gt;-->
                <!--                        &lt;!&ndash;                        <el-option label="停用" value="2"></el-option>&ndash;&gt;-->
                <!--                        <el-option v-for="dict in dicData"-->
                <!--                                   :key="dict.value"-->
                <!--                                   :label="dict.label"-->
                <!--                                   :value="dict.value"-->
                <!--                        ></el-option>-->
                <!--                    </el-select>-->
                <!--                </el-form-item>-->
                <el-form-item label="设备标识">
                    <el-input v-model="videoline.vlKey" placeholder="请输入设备标识"></el-input>
                </el-form-item>
                <el-form-item label="设备名称">
                    <el-input v-model="videoline.vlName" placeholder="请输入设备名称"></el-input>
                </el-form-item>
                <el-form-item label="上传人">
                    <el-input v-model="videoline.vlUser" placeholder="请输入上传人"></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="onSubmit" icon="el-icon-search">检索</el-button>
                </el-form-item>
            </el-form>
            <!--            <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">-->
            <!--                <el-form-item>-->
            <!--                    <el-button v-if="permissions.videoline_dovideoline_add" icon="el-icon-plus" type="primary"-->
            <!--                               @click="addOrUpdateHandle()">新增设备-->
            <!--                    </el-button>-->
            <!--                </el-form-item>-->
            <!--                <el-form-item style="float: right">-->
            <!--                    <el-button type="primary" @click="batchDelect" v-if="permissions.videoline_dovideoline_del"-->
            <!--                               :disabled="this.selectIds.length === 0" icon="el-icon-delete">删除-->
            <!--                    </el-button>-->
            <!--                </el-form-item>-->
            <!--                <el-form-item style="float: right">-->
            <!--                    <el-button type="primary" @click="stopVideoLine" :disabled="this.selectIds.length === 0"-->
            <!--                               icon="el-icon-close">停用-->
            <!--                    </el-button>-->
            <!--                </el-form-item>-->
            <!--                <el-form-item style="float: right">-->
            <!--                    <el-button type="primary" @click="startVideoLine" :disabled="this.selectIds.length === 0"-->
            <!--                               icon="el-icon-switch-button">启用-->
            <!--                    </el-button>-->
            <!--                </el-form-item>-->
            <!--            </el-form>-->


            <div class="avue-crud">
                <el-table
                        :data="dataList"
                        border
                        v-loading="dataListLoading"
                        :row-key="getRowKeys"
                        @selection-change="handleSelectionChange">
                    <el-table-column
                            type="selection"
                            width="55">
                    </el-table-column>
                    <el-table-column
                            prop="vlid"
                            header-align="center"
                            align="center"
                            label="主键"
                            v-if="false"
                    >
                    </el-table-column>
                    <el-table-column
                            prop="vlName"
                            header-align="center"
                            align="center"
                            label="设备名称">
                    </el-table-column>
                    <el-table-column
                            prop="vlKey"
                            header-align="center"
                            align="center"
                            label="设备标识">
                    </el-table-column>
                    <el-table-column
                            prop="vlUser"
                            header-align="center"
                            align="center"
                            label="上传人">
                    </el-table-column>
                    <el-table-column
                            prop="vlRtsp"
                            header-align="center"
                            align="center"
                            label="RTSP 流"
                            v-if="false">
                    </el-table-column>

                    <el-table-column
                            prop="remark"
                            header-align="center"
                            align="center"
                            label="备注"
                            v-if="false">
                    </el-table-column>
                    <el-table-column
                            prop="updateTime"
                            header-align="center"
                            align="center"
                            label="上传时间">
                    </el-table-column>
<!--                    <el-table-column-->
<!--                            header-align="center"-->
<!--                            align="center"-->
<!--                            label="操作">-->
<!--                        <template #="scope">-->
<!--                            <el-button v-if="permissions.videoline_dovideoline_edit" type="text" size="small"-->
<!--                                       icon="el-icon-edit" @click="updateHandle(scope.row.vlid)">修改-->
<!--                            </el-button>-->
<!--                            &lt;!&ndash;                            <el-button v-if="permissions.videoline_dovideoline_del" type="text" size="small"&ndash;&gt;-->
<!--                            &lt;!&ndash;                                       icon="el-icon-delete" @click="deleteHandle(scope.row.vlid)">删除&ndash;&gt;-->
<!--                            &lt;!&ndash;                            </el-button>&ndash;&gt;-->
<!--                        </template>-->
<!--                    </el-table-column>-->
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
            <upTableForm m v-if="updateVisible" ref="update" @refreshDataList="getDataList"></upTableForm>
        </basic-container>
    </div>
</template>

<script>
    import {fetchList, delObj, delObjs} from '@/api/dovideoline'
    import TableForm from './dovideoline-form.vue'
    import upTableForm from './updatevdline-form.vue'
    import {mapGetters} from 'vuex'
    import {startVideoLineByIdList, stopVideoLineByIdList} from "../../../api/dovideoline";

    export default {
        dictTypes: ['vl_status'],
        data() {
            return {
                tableData: [],//表格数据
                selectIds: [], //当前选框选中的值
                dataForm: {
                    key: ''
                },
                dicData: [{
                    label: '全部',
                    value: '0'
                }, {
                    label: '正常',
                    value: '1'
                }, {
                    label: '停用',
                    value: '2'
                }],
                videoline: {
                    vlKey: '',
                    vlName: '',
                    vlUser: ''
                },
                dataList: [],
                pageIndex: 1,
                pageSize: 10,
                totalPage: 0,
                dataListLoading: false,
                addOrUpdateVisible: false,
                updateVisible: false
            }
        },
        components: {
            TableForm, upTableForm
        },
        created() {
            this.getDataList()
            // console.log(scope)
            // console.log(fetchList)
            // console.log(this.dictTypes[''])
            // console.log(dict.type)
            // console.log(dict.type.zfjd_status)
        },
        computed: {
            ...mapGetters(['permissions'])
        },
        methods: {
            batchDelect() {
                // 删除前的提示
                this.$confirm("此操作将永久删除信息, 是否继续?", "提示", {
                    confirmButtonText: "确定",
                    cancelButtonText: "取消",
                    type: "warning"
                }).then(() => {
                    // 确定执行 then 方法
                    var idList = [];
                    // 遍历数组得到每个 id 值，设置到 idList 里面
                    for (var i = 0; i < this.selectIds.length; i++) {
                        var obj = this.selectIds[i];
                        var id = obj.vlid;
                        idList.push(id);
                    }
                    // 调用接口
                    delObjs(idList).then(response => {
                        // 提示
                        this.$message({
                            type: "success",
                            message: "删除成功!"
                        });
                        // 刷新页面
                        this.getDataList();
                    });
                });
            },
            startVideoLine() {
                // 启动前的提示
                this.$confirm("将启动所有已勾选的设备, 是否继续?", "提示", {
                    confirmButtonText: "确定",
                    cancelButtonText: "取消",
                    type: "warning"
                }).then(() => {
                    // 确定执行 then 方法
                    var idList = [];
                    // 遍历数组得到每个 id 值，设置到 idList 里面
                    for (var i = 0; i < this.selectIds.length; i++) {
                        var obj = this.selectIds[i];
                        var id = obj.vlid;
                        idList.push(id);
                    }
                    // 调用接口
                    startVideoLineByIdList(idList).then(response => {
                        // 提示
                        this.$message({
                            type: "success",
                            message: "启动成功!"
                        });
                        // 刷新页面
                        this.getDataList();
                    });
                });
            },
            stopVideoLine() {
                // 删除前的提示
                this.$confirm("将停止所有已勾选的设备, 是否继续?", "提示", {
                    confirmButtonText: "确定",
                    cancelButtonText: "取消",
                    type: "warning"
                }).then(() => {
                    // 确定执行 then 方法
                    var idList = [];
                    // 遍历数组得到每个 id 值，设置到 idList 里面
                    for (var i = 0; i < this.selectIds.length; i++) {
                        var obj = this.selectIds[i];
                        var id = obj.vlid;
                        idList.push(id);
                    }
                    console.log(idList)
                    // 调用接口
                    stopVideoLineByIdList(idList).then(response => {
                        // 提示
                        this.$message({
                            type: "success",
                            message: "停止成功!"
                        });
                        // 刷新页面
                        this.getDataList();
                    });
                });
            },
            getRowKeys(row) { //唯一值，一般都是id
                return row.vlid;
            },
            handleSelectionChange(selectIds) {
                this.selectIds = selectIds;
                console.log("选中的值", selectIds.map((item) => item.vlid));
            },
            onSubmit() {
                this.dataListLoading = true
                fetchList(Object.assign({
                    current: this.pageIndex,
                    size: this.pageSize,
                }, this.videoline)).then(response => {
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
                }, this.videoline)).then(response => {
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
            updateHandle(id) {
                this.updateVisible = true
                this.$nextTick(() => {
                    this.$refs.update.init(id)
                })
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
