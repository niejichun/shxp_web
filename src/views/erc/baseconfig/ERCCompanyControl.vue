<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
            <li><a href="javascript:;">基础数据维护</a></li>
            <li class="active">公司信息维护</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">公司列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <table id="companyTable"></table>
                    </div>
                </div>
            </div>
        </div>


        <div class="modal fade" id="AddModal" data-backdrop="static">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">公司信息</h4>
                    </div>
                    <form @submit.prevent="addDm" id="formA">
                        <div class="modal-body row">
                            <div class="row">

                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label"><span class="table-required">*</span>公司编号</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_code" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label"><span class="table-required">*</span>公司名称</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">经营范围</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_business_scope"  maxlength="150" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">主营业务</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_main_business"  maxlength="150" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">法人姓名</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_legal"  maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">法人身份证</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_legal_no"   maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">法人电话</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_agency_phone"   maxlength="30" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">ERC联系人</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_ERC_name"   maxlength="30" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">ERC联系人电话</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_ERC_phone"   maxlength="30" data-parsley-maxlength="50">
                                    </div>
                                </div>

                            </div>





                            <div class="row">
                                <div data-toggle="distpicker" id="distpicker" class="form-group">
                                    <div class="form-group col-sm-4">
                                        <label class="col-sm-3 control-label">省</label>
                                        <div class="form-group col-sm-9">
                                            <select class="form-control picker-width" id="province"></select>
                                        </div>
                                    </div>
                                    <div class="form-group col-sm-4">
                                        <label class="col-sm-3 control-label">市</label>
                                        <div class="form-group col-sm-9">
                                            <select class="form-control picker-width" id="city"></select>
                                        </div>
                                    </div>
                                    <div class="form-group col-sm-4">
                                        <label class="col-sm-3 control-label">区</label>
                                        <div class="form-group col-sm-9">
                                            <select class="form-control picker-width" id="district"></select>
                                        </div>
                                    </div>
                                </div>

                            </div>
                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">详细地址</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_adress"   maxlength="150" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">ERC联系人QQ</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_ERC_QQ"   maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>


                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">记账本位币</label>
                                    <div class="col-sm-9">
                                        <!--<input class="form-control" v-model="workRow.company_recording_currency"   maxlength="50" data-parsley-maxlength="50">-->
                                        <select class="form-control select2" id="company_recording_currency" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">外币</label>
                                    <div class="col-sm-9">
                                        <!--<input class="form-control" v-model="workRow.company_foreign"   maxlength="50" data-parsley-maxlength="50">-->
                                        <select class="form-control select2" id="company_foreign" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">价格精度</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_precision"   maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>


                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">利润诉求</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_profit_pursuit"   maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">供应商到货提前天数</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_advance_date"   maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">生产设备类固定资产确认标准</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_recognition_criteria"   maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>


                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">外包劳务简易采购流程金额标准</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_service_purchase_criteria"   maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">资产简易采购流程金额标准</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_property_purchase_criteria"   maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">复杂采购流程需要输入商家数</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_complex_supplier_number"   maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <label class="col-sm-3 control-label">计件员工保底金额</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.company_piece_amount"   maxlength="50" data-parsley-maxlength="50">
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
    const common = require('commonFunc')
    const apiUrl = '/api/erc/baseconfig/ERCCompanyControl?method='

    export default {
        data: function() {
            return {
                pagePara: '',
                workRow: {},
                oldRow: '',
                company_id:''
            }
        },
        name: 'companyControl',
        mounted: function() {
            $('#distpicker').distpicker({
                placeholder: true
            });
            this.initPage()
        },
        methods: {
            queryParams: function (params) {
                return JSON.stringify(params)
            },
            search: function(event) {
                $('#companyTable').bootstrapTable("refresh", this.queryParams)
            },
            addM: function(event) {
                this.workRow = {}
                this.company_id = ''
                $('#company_recording_currency').val(null).trigger('change');
                $('#company_foreign').val(null).trigger('change');
                $('#formA').parsley().reset()
                $('#distpicker').distpicker('reset', true);
                $('#AddModal') .modal('show')
            },
            addDm: function(event) {
                this.workRow.company_recording_currency = common.getSelect2Val('company_recording_currency');
                this.workRow.company_foreign = common.getSelect2Val('company_foreign');
                this.workRow.company_province = $('#province').val();
                this.workRow.company_city = $('#city').val();
                this.workRow.company_area = $('#district').val();
                if(this.company_id){
                    let params = {
                        old:{company_id:this.company_id},
                        new:this.workRow
                    }
                    this.$http.post(apiUrl + 'modifyCompany', params).then((response) => {
                        let retData = response.data.info;
                        this.workRow = {}
                        this.company_id = ''
                        common.dealSuccessCommon('修改成功')
                        $('#AddModal').modal('hide')
                        $('#companyTable').bootstrapTable("refresh", this.queryParams)
                    }, (response) => {
                        common.dealErrorCommon(this, response)
                    })
                }else {
                    if ($('#formA').parsley().isValid()) {
                        this.$http.post(apiUrl + 'addCompany', this.workRow).then((response) => {
                            let retData = response.data.info;
                            this.workRow = {}
                            common.dealSuccessCommon('增加成功')
                            $('#AddModal').modal('hide')
                            $('#companyTable').bootstrapTable("refresh", this.queryParams)
                        }, (response) => {
                            common.dealErrorCommon(this, response)
                        })
                    }
                }

            },
            initTable: function () {
                let _self = this
                window.tableEvents = {
                    'click .edit_detail': (e, value, row, index) => {
                        _self.workRow = $.extend(true, {}, row)
                        $('#company_recording_currency').val(row.company_recording_currency).trigger('change');
                        $('#company_foreign').val(row.company_foreign).trigger('change');
                        _self.company_id = row.company_id

                        $('#distpicker').distpicker('reset', true);
                        $('#distpicker').distpicker('destroy', true);
                        $('#distpicker').distpicker({
                            province: row.company_province,
                            city: row.company_city,
                            district: row.company_area
                        });

                        $('#AddModal') .modal('show')
                    },
                    'click .delete_company': (e, value, row, index) => {
                        common.dealConfrimCommon('确定要删除公司信息？', function() {
                            let params = {
                                company_id: row.company_id
                            }
                            _self.$http.post(apiUrl + 'delCompany', params).then((response) => {
                                common.dealSuccessCommon('删除成功')
                                $('#companyTable').bootstrapTable("refresh")
                            }, (response) => {
                                common.dealErrorCommon(_self, response)
                            })
                        })
                    },

                };
                $('#companyTable').bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getCompany',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFE('company_code', '供应商编号', (value, row) => {
                            return `<a class="show_detail" style="cursor: pointer;">${value}</a>`;
                        }, tableEvents),
                        common.BTRowFormatEditableLeft('company_name', '公司编号'),
                        common.BTRowFormatEditableLeft('company_business_scope', '公司名称'),
                        common.BTRowFormatEditable('company_main_business', '主营业务'),
                        common.BTRowFormatEditable('company_legal', '法人姓名'),
                        common.BTRowFormatEditable('company_legal_no', '法人身份证'),
                        common.BTRowFormatEditable('company_agency_phone', '法定联系电话'),
                        common.BTRowFormatEditable('company_ERC_name', 'ERC联系人'),
                        common.BTRowFormatEditable('company_ERC_phone', 'ERC联系电话'),
                        common.actFormatter('act', (value, row) => {
                            return [
                                '<a class="btn btn-info btn-xs m-r-5 edit_detail">编辑</a>',
                                '<a class="btn btn-info btn-xs m-r-5 delete_company">删除</a>'
                            ].join('')
                        }, tableEvents)
                    ],
                    idField: 'company_id',
                    uniqueId: 'company_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {
                        this.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        common.rowModifyWithT(this, apiUrl + 'modifyCompany', row, 'company_id', $('#companyTable'))
                    }
                })
                common.changeTableClass($('#companyTable'))
            },
            initPage: async function() {
                let response = await this.$http.post(apiUrl + 'init', {})
                let retData = response.data.info
                this.pagePara = retData;
                common.initSelect2($('#company_recording_currency'), this.pagePara.JZBWB);
                common.initSelect2($('#company_foreign'), this.pagePara.WB);
                this.initTable()
                $('#formA').parsley()
                common.reSizeCall()
            }
        }
    }
</script>
<style scoped>
    .p_r{
        position:relative;
    }
    .p_a{
        position:absolute;
        top:8px;
        right:0px;
    }
</style>
