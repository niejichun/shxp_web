<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">其他入库流水</a></li>
        <li class="active">其他入库流水单</li>
    </ol>

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">其他入库流水单</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control" multiple v-model="queryData.materiel" placeholder="搜索物料编码、物料名称" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <div class="row">
                        <div class="col-sm-3">
                            <div class="form-group">
                                <label class="col-sm-5 control-label">入库时间</label>
                                <div class="col-sm-7">
                                    <input class="form-control" v-model="orderData.created_at" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="col-sm-3">
                            <div class="form-group">
                                <label class="col-sm-5 control-label">申请订单号</label>
                                <div class="col-sm-7">
                                    <input class="form-control" v-model="orderData.bs_order_id" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="col-sm-3">
                            <div class="form-group">
                                <label class="col-sm-5 control-label">入库单号</label>
                                <div class="col-sm-7">
                                    <input class="form-control" v-model="orderData.bill_code" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="col-sm-3">
                            <div class="form-group">
                                <label class="col-sm-5 control-label">仓库</label>
                                <div class="col-sm-7">
                                    <select class="form-control select-width" v-model="orderData.warehouse_id" disabled>
                                        <option v-for="item in pagePara.wareHouseInfo" v-bind:value="item.id">{{item.text}}</option>
                                    </select>
                                </div>
                            </div>
                        </div>
                    </div>

                    <table id="historyTable"></table>

                    <!--<div class="row">
                        <div class="btn-group col-sm-12" style="padding: 10px 0px">
                            <button class="btn btn-info pull-right" @click="printDetail"><i class="fa fa-print"></i> 打印</button>
                        </div>
                    </div>-->
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
import Constant from '../../../assets/js/constant'
const common = require('commonFunc');
const apiUrl = '/api/erc/inventorymanage/ERCBuyInControl?method=';

export default {
    data: function() {
        return {
            masterTable: {},
            pagePara: {},
            queryData: {},
            orderData: {}
        }
    },
    name: 'ERCOtherInHistoryDetailControl',
    filters: {
        date_format(value) {
            return new Date(value).toLocaleDateString();
        }
    },
    methods: {
        queryParams: function(params) {
            params.materiel = this.queryData.materiel;
            params.bill_code = this.queryData.bill_code;
            params.start_date = this.queryData.start_date;
            params.end_date = this.queryData.end_date;
            return JSON.stringify(params);
        },
        initHistoryTable: function() {
            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getOtherInOrderHistoryDetail',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('p_order_id', '订单号'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'materielInfo'),
                    common.BTRowFormatEdSelect2DisabledAlignLeft(this, 'warehouse_zone_id', '仓区', 'wareHouseZoneInfo'),
                    common.BTRowFormatAlignRight('apply_price', '单价'),
                    common.BTRowFormatAlignRight('account_operate_amount', '入库数量'),
                    common.BTRowFormatWithFormatterAlignRight('total_price', '总价', (value, {apply_price, account_operate_amount}) => (apply_price * account_operate_amount)),
                    // common.BTRowFormatWithFormatterAlignRight('materiel_cost', '不含税单价',common.priceFormat),
                    // common.BTRowFormatAlignRight('materiel_tax', '税率'),
                    // common.BTRowFormatWithFormatterAlignRight('pure_cost', '不含税金额',common.priceFormat),
                    // common.BTRowFormatWithFormatterAlignRight('tax_cost', '含税金额',common.priceFormat),
                    // common.BTRowFormatWithFormatterAlignLeft('account_note', '备注',common.remarkFormatter)
                ],
                sortOrder: 'asc',
                idField: 'bill_code',
                uniqueId: 'bill_code',
                toolbar: '#toolbar',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageNumber: 1,
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass(this.masterTable);
        },
        initPage: function() {
            common.initDatepicker($('#start_date'));
            common.initDatepicker($('#end_date'));

            this.$http.post(apiUrl + 'init', {
                warehouse_id: this.orderData.warehouse_id
            }).then(response => {
                this.pagePara = response.data.info;
                console.log('init success:', this.pagePara);
                this.initHistoryTable();
            }, (response) => {
                console.log('init error');
                common.dealErrorCommon(this, response)
            });
        },
        queryConfirm: function() {
            this.queryData.start_date = $('#start_date').val();
            this.queryData.end_date = $('#end_date').val();
            console.log('queryConfirm:', this.queryData);
            this.masterTable.bootstrapTable("refresh", this.queryParams);
        },
        printDetail: async function() {
            let _self = this
            try {
                // let browser = common.BrowserType()
                // let filetype = 'image'
                // if (browser === 'Chrome' || browser === 'Safari') {
                //     filetype = 'pdf'
                // }
                let response = await _self.$http.post(apiUrl + 'OtherInOrderHistoryPrint', {
                    bill_code: _self.orderData.bill_code,
                    filetype: 'pdf'
                });
                let retData = response.data.info;
                printJS(retData.url)
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }

        }
    },
    mounted: function() {
        this.orderData = this.$route.query;
        this.queryData.bill_code = this.orderData.bill_code;
        this.orderData.created_at = new Date(this.orderData.created_at).toLocaleString();
        this.masterTable = $('#historyTable');
        this.initPage();
    }
}
</script>
<style scoped>

</style>
