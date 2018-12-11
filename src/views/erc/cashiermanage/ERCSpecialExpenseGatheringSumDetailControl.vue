<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">财务管理</a></li>
            <li class="active">财务单据汇总表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
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
                    <div class="panel-body ">
                        <div class="tab-content" v-show="tableIndex == 0" >
                            <table id="specialExpenseSumDetailTable" ></table>
                        </div>
                        <div class="tab-content" v-show="tableIndex == 1" >
                            <table id="cashiergatheringSumDetailTable" ></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/erc/cashiermanage/ERCSpecialExpenseGatheringSumControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                search_text:'',
                tableIndex:''
            }
        },
        name: 'ERCSpecialExpenseGatheringSumDetailControl',
        mounted: function() {
            let _self = this;
            let $specialExpenseSumDetailTable = $('#specialExpenseSumDetailTable');
            let $cashiergatheringSumDetailTable = $('#cashiergatheringSumDetailTable');

            function queryParamsSetS(params) {
                params.s_expense_confirm_time = _self.queryData.s_expense_sum_time;
                params.department_id = _self.queryData.department_id;
                params.s_capital_cost_type = _self.queryData.s_capital_cost_sum_type;
                return JSON.stringify(params)
            }
            function queryParamsSetC(params) {
                params.cashiergatheringsum_time = _self.queryData.cashiergatheringsum_time;
                params.department_id = _self.queryData.department_id;
                return JSON.stringify(params)
            }
            function initTableS() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('s_expense_confirm_time', '业务发生时间'),
                    common.BTRowFormat('department_name', '对应单位'),
                    common.BTRowFormat('bank_account', '银行账号'),
                    common.BTRowFormat('s_expense_content', '业务内容'),
                    common.BTRowFormatEdSelect2Disabled(_self, 's_capital_cost_type', '摘要', 'CAPITALCOSTTYLE'),
                    common.BTRowFormatWithFormatterAlignRight('s_sum_fee', '总金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('s_have_invoice_fee', '有发票金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('s_no_invoice_fee', '无发票金额',common.priceFormat),
                ];
                $specialExpenseSumDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getSpecialexpenseSumDetail',
                    queryParams: queryParamsSetS,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 's_expense_id',
                    uniqueId: 's_expense_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($specialExpenseSumDetailTable)
            }
            function initTableC() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('cashiergathering_examine_time', '业务发生时间'),
                    common.BTRowFormat('department_name', '对应单位'),
                    common.BTRowFormat('bank_account', '银行账号'),
                    common.BTRowFormat('cashiergatheringsum_content', '业务内容'),
                    common.BTRowFormat('cashiergatheringsum_digest', '摘要'),
                    common.BTRowFormatWithFormatterAlignRight('cashiergathering_gathering_money', '总金额',common.priceFormat),
                ];
                $cashiergatheringSumDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getCashiergatheringSumDetail',
                    queryParams: queryParamsSetC,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'cashiergathering_id',
                    uniqueId: 'cashiergathering_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($cashiergatheringSumDetailTable)
            }
            $(async function() {
                _self.queryData = _self.$route.query;
                _self.tableIndex = _self.queryData.tableIndex

                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                console.log(_self.pagePara)
                initTableS();
                initTableC();
            })
        },
        methods: {
            async onSearchBill() {
                if(this.queryData.tableIndex == 0){
                    $('#specialExpenseSumDetailTable').bootstrapTable("refresh");
                }else{
                    $('#cashiergatheringSumDetailTable').bootstrapTable("refresh");
                }
            }
        }
    }
</script>
<style scoped>

</style>
