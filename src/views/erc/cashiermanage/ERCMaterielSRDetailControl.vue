<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">物料收发汇总表</a></li>
        <li class="active">财务单据汇总表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <span class="panel-title">财务单据汇总表</span>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" v-model="search_text" placeholder="搜索对应单位名称、物料编码、物料名称" style="width: 200px;">
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" @click="onSearchBill"><i class="fa fa-search"></i></button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <table id="dataTable"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/erc/cashiermanage/ERCMaterielSRControl?method=';

    export default {
        data: function() {
            return {
                userinfo: common.getStoreData('userinfo'),
                apiName: common.getApiName(apiUrl),
                workRow: {},
                oldRow: '',
                queryData: null,
                pagePara: {},
                search_text: null
            }
        },
        name: 'ERCMaterielSRDetailControl',
        methods: {
            async onMenuClick({query}) {
                this.wmsType = query.wms_type;
                this.manageType = query.manage_type;
                $('#dataTable').bootstrapTable("refresh");
            },
            async getPagePara() {
                try {
                    let response = await this.$http.post(apiUrl + 'initMaterielData', {});
                    this.pagePara = response.data.info;
                } catch (error) {
                    common.dealErrorCommon(this, error);
                }
            },
            async onSearchBill() {
                console.log('onSearchNumber:', this.search_text);
                $('#dataTable').bootstrapTable("refresh");
            },
            async initTable(table){
                table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getMaterielBillItem',
                    queryParams: (params) => {
                        params.wms_type = this.queryData.wmsType;
                        params.manage_type = this.queryData.manageType;
                        params.bill_date = this.queryData.bill_date;
                        params.organization = this.queryData.organization;
                        params.search_text = this.search_text;
                        return JSON.stringify(params);
                    },
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('bill_date', '业务发生时间'),
                        common.BTRowFormat('organization', '对应单位'),
                        common.BTRowFormatWithFE('content', '对应内容', (value, {wms_type, manage_type}) => {
                            let content = '';
                            if (wms_type === 1) {
                                if (manage_type === 1) {
                                    content = '采购入库';
                                } else if (manage_type === 2) {
                                    content = '其他入库';
                                }
                            } else if (wms_type === 2) {
                                if (manage_type === 1) {
                                    content = '采购出库';
                                } else if (manage_type === 2) {
                                    content = '其他出库';
                                }
                            }
                            return `<div>${content}</div>`
                        }),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormat('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatAlignRight('store_amount', '数量'),
                        common.BTRowFormatAlignRight('store_price', '单价'),
                        common.BTRowFormatWithFormatterAlignRight('total_price', '金额', (value, {store_amount, store_price}) => {
                            return `<div>${store_amount * store_price}</div>`
                        }),
                    ],
                    idField: 'alldemand_id',
                    uniqueId: 'alldemand_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {
                        this.oldRow = $.extend(true, {}, row)
                    },
                });
                common.changeTableClass(table);
            },
        },
        async mounted() {
            this.queryData = this.$route.query;
            await this.getPagePara();
            await this.initTable($('#dataTable'));
        }
    }
</script>
<style scoped>

</style>
