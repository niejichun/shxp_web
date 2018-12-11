<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
        <li><a href="javascript:;">生成管理</a></li>
        <li class="active">生产工序列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">生产工序列表</h4>
                </div>
                <div class="form-inline panel-toolbar" role="form">
                    <div class="form-group">
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="搜索生产工序编码、名称" v-model="searchText" style="width: 180px">
                        </div>
                        <div class="form-group">
                            <button id="queryConfirm" class="btn btn-info" @click="queryConfirm">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>
                    </div>
                    <div class="form-group  pull-right">
                        <button id="showAddProcedure" class="btn btn-info" @click="showAddProcedure">增加工序
                        </button>
                    </div>
                    <div class="form-group  pull-right" style="position: relative;left :-20px">
                        <button id="showAddCoefficient" class="btn btn-info" @click="showAddCoefficient">增加系数
                        </button>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="margin-top: 0px">
                    <table id="tableProcedure"></table>
                </div>
            </div>
        </div>
    </div>


    <div class="modal fade" id="AddCoefficient">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增生产工序系数</h4>
                </div>
                <form @submit.prevent="saveCoefficient" id="formCoefficient">
                    <div class="modal-body row">
                        <div class="form-group col-xs-6">
                            <label>系数:</label>
                            <input type="text" class="form-control" v-model="workRow.coefficient">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>


    <div class="modal fade" id="AddProcedureModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增生产工序</h4>
                </div>
                <form @submit.prevent="saveProcedure" id="formProcedure">
                    <div class="modal-body row">
                        <div class="form-group col-xs-6">
                            <label class=""><span class="table-required">*</span>生产工序名称:</label>
                            <input class="form-control " v-model="workRow.procedure_name" data-parsley-required="true" maxlength="30" data-parsley-maxlength="30">
                        </div>
                        <div class="form-group col-xs-6">
                            <label>生产工序分类:</label>
                            <select class="form-control select2" multiple style="width:100%" id="procedure_type"
                                    data-parsley-required="true"></select>
                        </div>
                        <div class="form-group col-xs-6">
                            <label>生产工序工价:</label>
                            <input type="number" class="form-control" v-model="workRow.procedure_cost" data-parsley-type="number">
                        </div>
                        <div class="form-group col-xs-6">
                            <label>最低日工资:</label>
                            <input type="number" class="form-control" v-model="workRow.procedure_pay" data-parsley-type="number">
                        </div>
                        <div class="form-group col-xs-6">
                            <label>保底计算量:</label>
                            <input type="number" class="form-control" v-model="workRow.procedure_calc" data-parsley-type="number">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddDeviceModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增生产工序</h4>
                </div>
                <form @submit.prevent="addProductDevice" id="formAddDevice">
                    <div class="modal-body row">
                        <div class="form-group col-xs-6">
                            <label class=""><span class="table-required">*</span>生产工序名称:</label>
                            <div class="form-horizontal radio-container">
                                <div class="radio radio-inline radio-css radio-success">
                                    <input type="radio" name="cssRadio" id="fbType1" value="1"
                                           v-model="productDevice.device_level" data-parsley-required="true"/>
                                    <label for="fbType1">
                                        主设备
                                    </label>
                                </div>
                                <div class="radio radio-inline radio-css radio-success">
                                    <input type="radio" name="cssRadio" id="fbType2" value="2"
                                           v-model="productDevice.device_level" data-parsley-required="true"/>
                                    <label for="fbType2">
                                        辅设备
                                    </label>
                                </div>
                            </div>
                        </div>
                        <div class="form-group col-xs-6">
                            <label><span class="table-required">*</span>选择生产设备:</label>
                            <select class="form-control select2" multiple style="width:100%" id="product_device"
                                    data-parsley-required="true"></select>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>

    <div class="modal fade" id="showDeviceModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">生产设备列表</h4>
                </div>
                <div class="modal-body">
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th>设备级别</th>
                                <th>生产设备编号</th>
                                <th>生产设备名称</th>
                                <th>规格型号</th>
                                <th>计算单位</th>
                                <th>操作</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(item, index) in procedureDeviceList">
                                <td>{{['', '主设备', '从设备'][item.device_level]}}</td>
                                <td>{{item.fixedassets_no}}</td>
                                <td>{{item.fixedassets_name}}</td>
                                <td>{{item.fixedassets_model}}</td>
                                <td>{{item.fixedassets_unit}}</td>
                                <td>
                                    <button class="btn btn-danger btn-xs m-r-5" @click="onShowDeviceDelete(item, index)">删除</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="promptModal">
        <div class="modal-dialog modal-sm">
            <div class="modal-content">
                <!-- 模态框头部 -->
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal">&times;</button>
                    <h4 class="modal-title">删除生产设备</h4>
                </div>

                <!-- 模态框主体 -->
                <div class="modal-body">
                    确定要删除生产设备吗？
                </div>

                <!-- 模态框底部 -->
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>
                    <button type="button" class="btn btn-danger" data-dismiss="modal" @click="onDeviceDelete">确认</button>
                </div>

            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/erc/baseconfig/ERCProductProcedureControl?method=';

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            productDevice: {},
            deleteDevice: {},
            procedureDeviceList: [],
            workRow: {},
            oldRow: '',
            pagePara: {},
            searchedRow:[],
            searchText: null,
            searchData: null,
            tableProcedure: null,
            tableMateriel: null
        }
    },
    name: 'ERCProductProcedureControl',
    methods: {
        async getPagePara() {
            $('#formProcedure').parsley();
            try {
                let response = await this.$http.post(apiUrl + 'initProductionProcedure', {});
                this.pagePara = response.data.info;
                common.initSelect2Editable($('#procedure_type'), this.pagePara.procedureTypeInfo);
                common.initSelect2Editable($('#product_device'), this.pagePara.deviceInfo);
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async showAddCoefficient() {
            this.workRow = {};
            $('#AddCoefficient').modal('show')
        },
        async showAddProcedure() {
            this.workRow = {};
            $('#formProcedure').parsley().reset();
            $('#procedure_type').val(null).trigger('change');
            $('#AddProcedureModal').modal('show')
        },
        async showAddDevice({procedure_id}) {
            this.productDevice = {};
            this.productDevice.productprocedure_id = procedure_id;
            $('#formAddDevice').parsley().reset();
            $('#product_device').val(null).trigger('change');
            $('#AddDeviceModal').modal('show')
        },
        async showProductProcedureDevice({procedure_id}) {
            try {
                const { data } = await this.$http.post(apiUrl + 'getProductProcedureDevice', {productprocedure_id: procedure_id});
                if (data.errno === 0) {
                    this.procedureDeviceList = data.info.rows;
                    $('#showDeviceModal').modal('show')
                }
            } catch (error) {

            }
        },
        async addProductDevice() {
            try {
                if ($('#formAddDevice').parsley().isValid()) {
                    this.productDevice.productdevice_id = common.getSelect2Val('product_device');

                    await this.$http.post(apiUrl + 'addProductProcedureDevice', this.productDevice);
                    common.dealSuccessCommon('增加成功');
                    $('#AddDeviceModal').modal('hide');
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
                $('#AddDeviceModal').modal('hide')
            }
        },
        async onShowDeviceDelete({ppdevice_id}, index) {
            $('#promptModal').modal('show');
            this.deleteDevice.ppdevice_id = ppdevice_id;
            this.deleteDevice.index = index;
        },
        async onDeviceDelete() {
            try {
                const { data } = await this.$http.post(apiUrl + 'delProductProcedureDevice', {ppdevice_id: this.deleteDevice.ppdevice_id});
                if (data.errno === 0) {
                    this.procedureDeviceList.splice(this.deleteDevice.index, 1);
                    this.deleteDevice = {};
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async saveCoefficient() {
            try {
                if ($('#formCoefficient').parsley().isValid()) {


                    let reg = /^(0|[1-9][0-9]*)+(.[0-9]{1,2})?$/;
                    if(reg.test(this.workRow.coefficient)){
                        await this.$http.post(apiUrl + 'modifyCoefficient', this.workRow);
                        common.dealSuccessCommon('增加成功');
                        this.tableProcedure.bootstrapTable("refresh");
                        $('#AddCoefficient').modal('hide')
                    }else{
                        return common.dealPromptCommon('请输入正确数字')
                    }

                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async saveProcedure() {
            try {
                if ($('#formProcedure').parsley().isValid()) {
                    this.workRow.procedure_type = common.getSelect2Val('procedure_type');

                    await this.$http.post(apiUrl + 'addProductionProcedure', this.workRow);
                    common.dealSuccessCommon('增加成功');
                    this.tableProcedure.bootstrapTable("refresh");
                    $('#AddProcedureModal').modal('hide')
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        queryConfirm() {
            this.tableProcedure.bootstrapTable("refresh")
        },
        async initTableProcedure(){
            window.tableEvents = {
                'click .product_device': async (e, value, row, index) => {
                    await this.showProductProcedureDevice(row);
                },
                'click .add_device': async (e, value, row, index) => {
                    await this.showAddDevice(row);
                },
                'click .delete_procedure': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '生产工序删除', apiUrl + 'deleteProductionProcedure', this.tableProcedure, row, 'procedure_id', function(){})
                }
            };
            this.tableProcedure.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProductionProcedure',
                queryParams: (params) => {
                    params.search_text = this.searchText;
                    return JSON.stringify(params)
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('procedure_code', '生产工序编号'),
                    common.BTRowFormatAlignLeft('procedure_name', '生产工序名称'),
                    common.BTRowFormatEdSelect2(this, 'procedure_type', '生产工序分类', 'procedureTypeInfo'),
                    common.BTRowFormatEdPrice('procedure_cost', '生产工序工价',common.priceFormat),
                    common.BTRowFormatEdPrice('procedure_pay', '最低日工资',common.priceFormat),
                    common.BTRowFormatEditableRight('procedure_calc', '保底计算量'),
                    common.BTRowFormat('procedure_coefficient', '系数'),
                    // common.BTRowFormatEdSelectAlignLeft(this, 'procedure_master_device', '对应主生产设备', 'deviceInfo'),
                    // common.BTRowFormatEdSelectAlignLeft(this, 'procedure_slave_device', '对应辅生产设备', 'deviceInfo'),
                    common.actFormatter('act', () => {
                        return `<button class="btn btn-info btn-xs m-r-5 product_device">显示生产设备</button>
                                <button class="btn btn-info btn-xs m-r-5 add_device">添加生产设备</button>
                                <button class="btn btn-info btn-xs m-r-5 delete_procedure">删除</button>`
                    }, tableEvents)
                ],
                idField: 'procedure_id',
                uniqueId: 'procedure_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row);
                },
                onEditableSave: (field, row, oldValue, $el) => {
                    common.rowModifyWithT(this, apiUrl + 'modifyProductionProcedure', row, 'procedure_id', this.tableProcedure);
                }
            });
            common.changeTableClass(this.tableProcedure)
        }
    },
    async mounted() {
        this.tableProcedure = $('#tableProcedure');
        await this.getPagePara();
        await this.initTableProcedure();
        common.reSizeCall();
    },
}
</script>
<style scoped>
    .m_t{
        margin-top: 15px;
        /*margin-left: 10px;*/
        /*margin-right: 10px;*/
    }
</style>
