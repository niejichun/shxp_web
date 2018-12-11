<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
            <li><a href="javascript:;">生产计划管理</a></li>
            <li class="active">生产计划列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">生产计划列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">

                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text" placeholder="生产任务单号、销售单号" style="width: 230px;">
                                </div>
                            </div>
                            <div class="form-group">
                                <input type="text" class="form-control" id="start_date" placeholder="开始时间" />
                            </div>
                            <div class="form-group">
                                <span>-</span>
                                <input type="text" class="form-control" id="end_date" placeholder="结束时间" />
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body" style="padding: 15px">
                        <div  id="set_table">
                            <table id="setTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/erc/productionmanage/ERCProductPlanExecuteControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
            }
        },
        name: 'ERCProductPlanExecuteControl',
        mounted: function() {
            let _self = this;
            let $setTable = $('#setTable');

            function queryParamsSet(params) {
                params.search_text=$('#search_text').val();
                if ($('#start_date').val()) {
                    params.beginDate = $('#start_date').val()
                }
                if ($('#end_date').val()) {
                    params.endDate = $('#end_date').val()
                }
                return JSON.stringify(params)
            }

            function initsetTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('m1_materiel_code', '最终产品编码'),
                    common.BTRowFormat('m1_materiel_name', '最终产品名称'),
                    common.BTRowFormat('productivetask_code', '生产任务单号'),
                    common.BTRowFormat('m2_materiel_code', '生产任务单产品编号'),
                    common.BTRowFormat('m2_materiel_name', '生产任务单产品名称'),
                    common.BTRowFormat('taskdesign_number', '生产数量'),
                    common.BTRowFormatAlignLeft('product_level', '层级'),
                    common.BTRowFormat('order_id', '销售订单号'),
                    common.BTRowFormat('procedure_name', '工序名称'),
                    common.BTRowFormat('workshop_id', '生产车间'),
                    common.BTRowFormat('begin_date', '开始日期'),
                    common.BTRowFormat('end_date', '结束日期'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'productivetask_state', '施工状态', 'productivetaskstate')
                ];
                $setTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getPlanExecute',
                    queryParams: queryParamsSet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'productivetask_id',
                    uniqueId: 'productivetask_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'

                });
                common.changeTableClass($setTable)
            }

            async function initData() {
                common.initDatepicker($('#start_date'))
                common.initDatepicker($('#end_date'))

                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                initsetTable();
            }
            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#setTable').bootstrapTable("refresh");
            }
        }
    }
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
