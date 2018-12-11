<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
        <li><a href="javascript:;">生产管理</a></li>
        <li class="active">生产设备列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">生产设备列表</h4>
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
                        <button id="showAddProcedure" class="btn btn-info" @click="showAddProcedure">增加
                        </button>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="margin-top: 0px">
                    <table id="tableProductDevice"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddProcedureModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增生产设备</h4>
                </div>
                <form @submit.prevent="addProductDevice" id="formProductDevice">
                    <div class="modal-body row">
                        <div class="form-group col-xs-6">
                            <label>选择固定资产:</label>
                            <select class="form-control select2" multiple style="width:100%" id="fixed_assets"
                                    data-parsley-required="true"></select>
                        </div>
                        <div class="form-group col-xs-6">
                            <label>日产能:</label>
                            <input type="number" class="form-control" v-model="workRow.day_capacity" data-parsley-type="number">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/erc/baseconfig/ERCProductDeviceControl?method=';

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            pagePara: {},
            searchedRow:[],
            searchText: null,
            searchData: null,
            tableProductDevice: null,
            tableMateriel: null
        }
    },
    name: 'ERCProductProcedureControl',
    methods: {
        async getPagePara(event) {
            $('#formProductDevice').parsley();
            try {
                let response = await this.$http.post(apiUrl + 'initProductDevice', {});
                this.pagePara = response.data.info;
                common.initSelect2Editable($('#procedure_type'), this.pagePara.procedureTypeInfo);
                common.initSelect2Editable($('#fixed_assets'), this.pagePara.deviceInfo);
                common.initSelect2Editable($('#slave_device'), this.pagePara.deviceInfo);
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async showAddProcedure() {
            this.workRow = {};
            $('#formProductDevice').parsley().reset();
            $('#procedure_type').val(null).trigger('change');
            $('#fixed_assets').val(null).trigger('change');
            $('#slave_device').val(null).trigger('change');
            $('#AddProcedureModal').modal('show')
        },
        async addProductDevice() {
            try {
                if ($('#formProductDevice').parsley().isValid()) {
                    this.workRow.fixedassetsdetail_id = common.getSelect2Val('fixed_assets');
                    console.log('addProductDevice:', this.workRow);

                    await this.$http.post(apiUrl + 'addProductDevice', this.workRow);
                    common.dealSuccessCommon('增加成功');
                    this.tableProductDevice.bootstrapTable("refresh");
                    $('#AddProcedureModal').modal('hide')
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        queryConfirm(event) {
            this.tableProductDevice.bootstrapTable("refresh")
        },
        async initTableProductDevice(event){
            window.tableEvents = {
                'click .delete_device': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '生产设备删除', apiUrl + 'deleteProductDevice', this.tableProductDevice, row, 'productdevice_id', function(){})
                }
            };
            this.tableProductDevice.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProductDevice',
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
                    common.BTRowFormat('fixedassets_no', '生产设备编号'),
                    common.BTRowFormat('fixedassets_name', '生产设备名称'),
                    common.BTRowFormat('fixedassets_model', '规格型号'),
                    common.BTRowFormat('fixedassets_unit', '计算单位'),
                    common.BTRowFormatEditableRight('day_capacity', '日产能'),
                    common.actFormatter('act', () => {
                        return `<button class="btn btn-info btn-xs m-r-5 delete_device">删除</button>`
                    }, tableEvents)
                ],
                idField: 'productdevice_id',
                uniqueId: 'productdevice_id',
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
                    common.rowModifyWithT(this, apiUrl + 'modifyProductDevice', row, 'productdevice_id', this.tableProductDevice);
                }
            });
            common.changeTableClass(this.tableProductDevice)
        }
    },
    async mounted() {
        this.tableProductDevice = $('#tableProductDevice');
        await this.getPagePara();
        await this.initTableProductDevice();
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
