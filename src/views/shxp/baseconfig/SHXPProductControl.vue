<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
        <li><a href="javascript:;">小程序基础信息维护</a></li>
        <li class="active">菜单设置</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">菜单列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                        </div>
                        <div class="form-group  pull-right">
                            <button id="addM" class="btn btn-info" v-on:click="addM">增加
                      </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="productTable"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddModal" data-backdrop="static">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新建菜品</h4>
                </div>
                <form @submit.prevent="addAct" id="formA">
                    <div class="modal-body row">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>菜品名称</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.product_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>所属类别</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" id="product_class" data-parsley-required="true"></select>
                                </div>
                            </div>
                        </div>

                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>单价</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.product_price" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>是否推荐菜</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" id="product_recommend" data-parsley-required="true"></select>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/shxp/baseconfig/SHXPProductControl?method=';

    export default {
        data: function() {
            return {
                workRow: {},
                oldRow: '',
                pagePara: {}
            }
        },
        name: 'SHXPProductControl',
        mounted: async function() {
            this.productTable = $('#productTable');
            this.initData();
        },
        methods: {
            queryParams: function (params) {
                return JSON.stringify(params)
            },
            initTable: function () {
                window.tableEvents = {

                };
                this.productTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchProduct',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [

                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('product_code', '菜品编号'),
                        common.BTRowFormatEditable('product_name', '菜品名称'),
                        common.BTRowFormatEdSelect2(this, 'product_class', '所属类别', 'shxpProductClass'),
                        common.BTRowFormatEditable('product_price', '单价'),
                        common.BTRowFormatWithFormatter('file_url', '菜品图片', common.imageViewerFormatter),
                        common.BTRowFormatEdSelect2(this, 'product_recommend', '是否推荐菜', 'shxpProductRecommend'),
                        common.BTRowFormat('created_at', '创建日期')
                    ],
                    idField: 'product_id',
                    uniqueId: 'product_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {
                        this.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        common.rowModifyWithT(this, apiUrl + 'modifyProduct', row, 'product_id', this.masterTable)
                    }
                })
                common.changeTableClass(this.productTable)
            },
            initData: async function () {
                let response = await this.$http.post(apiUrl + 'init', {});
                this.pagePara = response.data.info;
                common.initSelect2($('#product_class'), this.pagePara.shxpProductClass);
                common.initSelect2($('#product_recommend'), this.pagePara.shxpProductRecommend);
                $('#formA').parsley();
                this.initTable();
                common.reSizeCall();
            },
            addM: function(event) {
                this.workRow = {};
                $('#product_class').val(null).trigger('change');
                $('#product_recommend').val(null).trigger('change');
                $('#formA').parsley().reset();
                $('#AddModal').modal('show')
            },
            addAct: async function(event) {
                try {
                    if ($('#formA').parsley().isValid()) {
                        this.workRow.product_class = common.getSelect2Val('product_class')
                        this.workRow.product_recommend = common.getSelect2Val('product_recommend')
                        let response = await this.$http.post(apiUrl + 'addProduct', this.workRow);
                        common.dealSuccessCommon('增加成功');
                        this.productTable.bootstrapTable("refresh");
                        $('#AddModal').modal('hide')
                    }
                } catch (error) {
                    common.dealErrorCommon(this, error);
                }
            },
            queryConfirm: function(event) {
                this.productTable.bootstrapTable("refresh")
            }
        }
    }
</script>
<style scoped>
</style>
