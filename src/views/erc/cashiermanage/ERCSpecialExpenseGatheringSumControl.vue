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
                            <div class="pull-right">
                                <div class="form-group">
                                    <div class="form-group">
                                        <input type="text" class="form-control" id="search_date" v-model="search_date" placeholder="汇总时间" />
                                    </div>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" @click="dataExtract">生成汇总数据</button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(0)" id="name1">
                                <a href="#specialExpenseSum" data-toggle="tab">
                                    <span>资金支出汇总表</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(1)" id="name">
                                <a href="#subscribe" data-toggle="tab">
                                    <span>客户收款汇总表</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content" >
                            <div class="tab-pane fade active in" id="specialExpenseSum">
                                <table id="specialExpenseSumTable"></table>
                            </div>
                            <div class="tab-pane fade" id="subscribe">
                                <table id="cashiergatheringSumTable"></table>
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
    const apiUrl = '/api/erc/cashiermanage/ERCSpecialExpenseGatheringSumControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                search_date:'',
                search_text:'',
                tableIndex:0
            }
        },
        name: 'ERCSpecialExpenseGatheringSumControl',
        mounted: function() {
            let _self = this;
            let $specialExpenseSumTable = $('#specialExpenseSumTable');
            let $cashiergatheringSumTable = $('#cashiergatheringSumTable');
            function queryParamsSet(params) {
                return JSON.stringify(params)
            }
            function initTableS() {
                window.tableEvents = {
                    'click .showS_detail': (e, value, row, index) => {
                        console.log(1234)
                        _self.$router.push({
                            path: '/erc/cashiermanage/ERCSpecialExpenseGatheringSumDetailControl',
                            query: {
                                s_expense_sum_time: row.s_expense_sum_time,
                                department_id: row.s_expense_sum_depart_id,
                                s_capital_cost_sum_type: row.s_capital_cost_sum_type,
                                tableIndex:_self.tableIndex
                            }
                        })
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('s_expense_sum_code', '费用支出汇总表单号', (value, row) => {
                        return `<a class="showS_detail" style="cursor: pointer;">${value}</a>`;
                    }, tableEvents),
                    common.BTRowFormat('s_expense_sum_time', '业务时间'),
                    common.BTRowFormat('s_expense_sum_content', '业务内容'),
                    common.BTRowFormatWithFormatterAlignRight('s_expense_sum_amount', '总金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('s_have_invoice_sum_fee', '有发票金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('s_no_invoice_sum_fee', '无发票金额',common.priceFormat),
                ];
                $specialExpenseSumTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getSpecialexpenseSum',
                    queryParams: queryParamsSet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 's_expense_sum_depart_id',
                    uniqueId: 's_expense_sum_depart_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($specialExpenseSumTable)
            }
            function initTableC() {
                window.tableEvents = {
                    'click .showC_detail': (e, value, row, index) => {
                        _self.$router.push({
                            path: '/erc/cashiermanage/ERCSpecialExpenseGatheringSumDetailControl',
                            query: {
                                cashiergatheringsum_time: row.cashiergatheringsum_time,
                                department_id: row.cashiergatheringsum_depart_id,
                                tableIndex:_self.tableIndex
                            }
                        })
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('cashiergatheringsum_code', '客户收款汇总表单号', (value, row) => {
                        return `<a class="showC_detail" style="cursor: pointer;">${value}</a>`;
                    }, tableEvents),
                    common.BTRowFormat('cashiergatheringsum_time', '业务时间'),
                    common.BTRowFormat('cashiergatheringsum_content', '业务内容'),
                    common.BTRowFormatWithFormatterAlignRight('cashiergatheringsum_amount', '总金额',common.priceFormat)
                ];
                $cashiergatheringSumTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getCashiergatheringSum',
                    queryParams: queryParamsSet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'cashiergatheringsum_depart_id',
                    uniqueId: 'cashiergatheringsum_depart_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($cashiergatheringSumTable)
            }
            $(function() {
                common.initDatepicker($('#search_date'));
                initTableS();
                initTableC();
            })
        },
        methods: {
            async showTable(index){
                this.tableIndex = index
            },
            async onSearchBill() {
                if(this.tableIndex == 0){
                    $('#specialExpenseSumTable').bootstrapTable("refresh");
                }else{
                    $('#cashiergatheringSumTable').bootstrapTable("refresh");
                }

            },
            async dataExtract() {
                this.search_date = $('#search_date').val();
                try {
                    if(this.tableIndex == 0){
                        const result = await this.$http.post(apiUrl + 'dataExtractS', {search_date: this.search_date}).then(resp => resp.data);
                        if (result.errno === 0) {
                            common.dealSuccessCommon('生成成功');
                            $('#specialExpenseSumTable').bootstrapTable("refresh");
                        }
                    }else{
                        const result = await this.$http.post(apiUrl + 'dataExtractC', {search_date: this.search_date}).then(resp => resp.data);
                        if (result.errno === 0) {
                            common.dealSuccessCommon('生成成功');
                            $('#cashiergatheringSumTable').bootstrapTable("refresh");
                        }
                    }
                } catch (error) {
                    common.dealErrorCommon(this, error);
                }
            },

        }
    }
</script>
<style scoped>

</style>
