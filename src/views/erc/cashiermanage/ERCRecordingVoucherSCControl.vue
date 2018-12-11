<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">财务管理</a></li>
            <li class="active">资金费用记账凭证</li>
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
                        <div class="tab-content" >
                            <table id="recordingvoucherscTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/erc/cashiermanage/ERCRecordingVoucherSCControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                search_text:'',
            }
        },
        name: 'ERCRecordingVoucherSCControl',
        mounted: function() {
            let _self = this;
            let $recordingvoucherscTable = $('#recordingvoucherscTable');
            function queryParamsSet(params) {
                return JSON.stringify(params)
            }
            function initTable() {
                window.tableEvents = {
                    'click .showS_detail': (e, value, row, index) => {
                        _self.$router.push({
                            path: '/erc/cashiermanage/ERCRecordingVoucherDetailSCControl',
                            query: {
                                recordingvouchersc_depart_id: row.recordingvouchersc_depart_id,
                                recordingvouchersc_time: row.recordingvouchersc_time,
                                recordingvouchersc_type:row.recordingvouchersc_type,
                                s_recordingvouchersc_type:row.s_recordingvouchersc_type
                            }
                        })
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('recordingvouchersc_code', '单号', (value, row) => {
                        return `<a class="showS_detail" style="cursor: pointer;">${value}</a>`;
                    }, tableEvents),
                    common.BTRowFormat('order_type', '单据类型'),
                    common.BTRowFormat('department_name', '对应单位'),
                    common.BTRowFormat('recordingvouchersc_time', '生成时间'),
                    common.BTRowFormat('recordingvouchersc_count', '单据张数')
                ];
                $recordingvoucherscTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getRecordingVoucherSC',
                    queryParams: queryParamsSet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'recordingvouchersc_id',
                    uniqueId: 'recordingvouchersc_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($recordingvoucherscTable)
            }
            $(function() {
                initTable();
            })
        },
        methods: {
            async onSearchBill() {
                $('#recordingvoucherscTable').bootstrapTable("refresh");
            }
        }
    }
</script>
<style scoped>

</style>
