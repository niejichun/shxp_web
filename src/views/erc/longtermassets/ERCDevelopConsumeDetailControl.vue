<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">研发资产管理</a></li>
            <li class="active">材料耗用单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">材料耗用单</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索物料名称">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <table id="consumeDevelopDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/erc/longtermassets/ERCDevelopConsumeControl?method=';
    export default {
        data: function() {
            return {
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                amortizereceive_id:"",
                state:''
            }
        },
        name: 'ERCDevelopConsumeDetailControl',
        mounted: function() {
            let _self = this;
            _self.developconsume_id = _self.$route.query.developconsume_id;
            let $consumeDevelopDetailTable = $('#consumeDevelopDetailTable');
            // if(_self.amortizeconsume_id === undefined){
            //     _self.state = 2
            // }
            // if(_self.developconsume_id === undefined){
            //     _self.state = 1
            // }

            function queryDevelopParams(params) {
                params.search_text=$('#search_text').val();
                params.developconsume_id=_self.developconsume_id;

                return JSON.stringify(params)
            }
            function initDevelopTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribe_name', '物料名称'),
                    common.BTRowFormat('subscribe_format', '规格型号'),
                    common.BTRowFormat('subscribe_unit', '计量单位'),
//                    common.BTRowFormat('supplier_name', '供应商名称'),
                    common.BTRowFormatAlignRight('consumedetail_number', '数量'),
                    common.BTRowFormatWithFormatterAlignRight('consumedetail_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('consumedetail_money', '金额',common.priceFormat)
                ];
                $consumeDevelopDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchDevelopConsumeDetail',
                    queryParams: queryDevelopParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'developconsumedetail_id',
                    uniqueId: 'developconsumedetail_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',

                });
                common.changeTableClass($consumeDevelopDetailTable)
            }

            $(function() {
                initDevelopTable();

                common.reSizeCall();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#consumeDevelopDetailTable').bootstrapTable("refresh");
            }
        }
    }
</script>
<style scoped>
</style>
