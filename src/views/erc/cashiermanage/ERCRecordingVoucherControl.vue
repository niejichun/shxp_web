<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
        <li><a href="javascript:;">财务管理</a></li>
        <li class="active">记账凭证</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <span class="panel-title">记账凭证</span>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" v-model="search_text" placeholder="搜索单号、单据类型" style="width: 200px;">
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
            pagePara: {},
            search_text: null,
        }
    },
    name: 'ERCMaterielSRControl',
    methods: {
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
        async initTable(table) {
            window.tableEvents = {
                'click .show_detail': (e, value, {record_date, wms_type, manage_type, organization, org_type, total_price, paper_count}, index) => {
                    this.$router.push({
                        path: '/erc/cashiermanage/ERCRecordingVoucherDetailControl',
                        query: {
                            record_date,
                            wms_type,
                            manage_type,
                            organization,
                            org_type,
                            total_price,
                            paper_count
                        }
                    })
                }
            };

            table.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getRecordingVoucherCollection',
                queryParams: (params) => {
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
                    common.BTRowFormatWithFE('record_code', '单号', (value, {record_date}, index) => {
                        function pad(num, n) {
                            let len = num.toString().length;
                            while(len < n) {
                                num = "0" + num;
                                len++;
                            }
                            return num;
                        }
                        const [year, month] = record_date.split('-');
                        return `<a class="show_detail">转${year.slice(2, year.length)}${month}${pad(index + 1, 8)}</a>`
                    }, tableEvents),
                    common.BTRowFormatWithFE('record_type', '单据类型', (value, {wms_type, manage_type}) => {
                        if (wms_type === 1 && manage_type === 1) {
                            return `<div>采购入库单</div>`;
                        } else if (wms_type === 1 && manage_type === 3) {
                            return `<div>其他入库单</div>`;
                        } else if (wms_type === 2 && manage_type === 1) {
                            return `<div>销售出库单</div>`;
                        } else if (wms_type === 2 && manage_type === 3) {
                            return `<div>其他出库单</div>`;
                        }
                    }, tableEvents),
                    common.BTRowFormat('organization', '对应单位'),
                    common.BTRowFormatAlignRight('total_price', '总金额'),
                    common.BTRowFormatAlignRight('paper_count', '单据张数'),
                    common.BTRowFormat('record_date', '生成时间'),
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
        common.initDatepicker($('#bill_date'));
        await this.getPagePara();
        await this.initTable($('#dataTable'));
    }
}
</script>
<style scoped>

</style>
