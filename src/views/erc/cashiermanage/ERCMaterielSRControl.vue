<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
        <li><a href="javascript:;">财务管理</a></li>
        <li class="active">物料收发汇总表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <span class="panel-title">物料收发汇总表</span>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" v-model="search_text" placeholder="搜索单号、单据类型" style="width: 200px;">
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" @click="onSearchBill"><i class="fa fa-search"></i></button>
                        </div>
                        <div class="pull-right">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" id="bill_date" v-model="bill_date" placeholder="汇总时间" />
                                </div>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" @click="onGenerateBill">生成汇总数据</button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <ul class="nav nav-tabs">
                        <li :class="index === 0 ? 'active' : ''" v-for="(item, index) in buttonMenu" @click="onMenuClick(item)">
                            <a href="#nav-tab" data-toggle="tab">{{item.name}}</a>
                        </li>
                    </ul>
                    <div class="tab-content" style="padding: 15px">
                        <div class="tab-pane fade active in" id="nav-tab">
                            <table id="dataTable"></table>
                        </div>
                    </div>
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
            buttonMenu: [
                {
                    name: '采购入库单',
                    query: {
                        wms_type: 1,
                        manage_type: 1
                    }
                },
                {
                    name: '生产入库单',
                    query: {
                        wms_type: 1,
                        manage_type: 2
                    }
                },
                {
                    name: '其它入库单',
                    query: {
                        wms_type: 1,
                        manage_type: 3
                    }
                },
                {
                    name: '销售出库单',
                    query: {
                        wms_type: 2,
                        manage_type: 1
                    }
                },
                {
                    name: '产品领料单',
                    query: {
                        wms_type: 2,
                        manage_type: 2
                    }
                },
                {
                    name: '其它出库单',
                    query: {
                        wms_type: 2,
                        manage_type: 3
                    }
                }
            ],
            workRow: {},
            oldRow: '',
            pagePara: {},
            search_text: null,
            bill_date: null,
            wmsType: 1,
            manageType: 1,
        }
    },
    name: 'ERCMaterielSRControl',
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
        async onGenerateBill() {
            this.bill_date = $('#bill_date').val();
            console.log('onGenerateBill:', this.bill_date);

            try {
                const result = await this.$http.post(apiUrl + 'genMaterielCollectionData', {bill_date: this.bill_date}).then(resp => resp.data);
                if (result.errno === 0) {
                    common.dealSuccessCommon('生成成功');
                    $('#dataTable').bootstrapTable("refresh");
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async initTable(table){
            window.tableEvents = {
                'click .show_detail': (e, value, row, index) => {
                    this.$router.push({
                        path: '/erc/cashiermanage/ERCMaterielSRDetailControl',
                        query: {
                            bill_date: row.bill_date,
                            organization: row.organization,
                            wms_type: this.wms_type,
                            manage_type: this.manage_type
                        }
                    })
                }
            };

            table.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getMaterielCollection',
                queryParams: (params) => {
                    params.wms_type = this.wmsType;
                    params.manage_type = this.manageType;
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
                    common.BTRowFormatWithFE('financerecord_code', '物料收发汇总单号', (value, row) => {
                        return `<a class="show_detail">${value}</a>`;
                    }, tableEvents),
                    common.BTRowFormat('bill_date', '业务发生时间'),
                    common.BTRowFormat('organization', '对应单位'),
                    common.BTRowFormatAlignRight('total_price', '金额'),
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
