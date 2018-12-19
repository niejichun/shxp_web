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

                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>上传照片</label>
                                <div class="col-sm-8">
                                    <input id='designUpload' type="file" name="file" style="position:relative;top: 7px;">
                                    <div  v-for="(file,index) in files" class="img-div" >
                                        <a :href=file.file_url target="_blank">
                                            <img class="consumables-img" :src="file.file_url" alt="">
                                        </a>
                                        <i class="glyphicon glyphicon-remove-sign delete_icon" v-on:click="removeImage(index,file)" ></i>
                                    </div>


                                    <!--<a :href=files.file_url target="_blank">-->
                                        <!--<img class="consumables-img" :src="files.file_url" alt="">-->
                                    <!--</a>-->
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
                pagePara: {},
                files:[]

            }
        },
        name: 'SHXPProductControl',
        mounted: async function() {
            let _self = this;
            _self.productTable = $('#productTable');
            _self.initData();


            $(function(){
                common.fileUpload(_self, $('#designUpload'), apiUrl, function(fileInfo) {
                    _self.files.push({
                        file_url:fileInfo.url,
                        file_name:fileInfo.name
                    })
                });
            })

        },
        methods: {
            removeImage:function (index,file) {
                let _self=this;
                _self.files.splice(index, 1);
                console.log('file.file_url',file.file_url)
                if(file.file_url){
                    var params={
                        product_img_url:file.file_url
                    };
                    _self.$http.post(apiUrl + 'removeFile', params).then(response =>{
                        if(response.body.errno==0){
                            this.productTable.bootstrapTable("refresh");
                            $('#AddModal').modal('hide')
                        }else {
                            console.log(response.msg)
                        }
                    },(response) =>{
                        common.dealErrorCommon(response);
                    })
                }
            },
            queryParams: function (params) {
                return JSON.stringify(params)
            },

            initTable: function () {
                let _self = this
                // window.designEvents = {
                //     'change .fileupload': function(e, value, row, index) {
                //         common.fileFileUpload(this, _self, row, apiUrl, 'inter_review_update', $table, 'internal_review_id')
                //     },
                //     'click .delete-button': function(e, value, row, index) {
                //         common.deleteFiles(this, _self, row, apiUrl, 'delete_file', $table, 'internal_review_id')
                //     }
                // }

                window.tableEvents = {
                    "click .delete" :async function (e, value, row, index){
                        let param = {
                            product_id:row.product_id,
                            product_img_url:row.product_img_url
                        }
                        let response = await _self.$http.post(apiUrl + 'deleteProduct', param);
                        common.dealSuccessCommon('删除成功');
                        _self.productTable.bootstrapTable("refresh");

                    },
                }
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
                        // common.BTRowFormatWithFormatter('file_url', '菜品图片', common.imageViewerFormatter),
                        common.BTRowFormatWithFormatter('product_img_url', '菜品图片', common.fileFormatter),
                        // common.BTRowFormatWithFE('files', '文件', common.filesFormatterWithUpload, designEvents),
                        common.BTRowFormatEdSelect2(this, 'product_recommend', '是否推荐菜', 'shxpProductRecommend'),
                        common.BTRowFormat('created_at', '创建日期'),
                        common.actFormatter('act', ()=>{
                            return [
                                '<a class="btn btn-info btn-xs m-r-5 delete">删除</a>'
                            ].join('')
                        }, tableEvents)
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
                this.files = []
                $('#designUpload').val('')
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
                        if(this.files && this.files.length>0){
                            this.workRow.product_img_url = this.files[0].file_url;
                        }
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
    .consumables-img{
        width: 70px;
        height: 70px;
    }
    .img-div{
        margin-top: 24px;
        position: relative;
        font-size:16px;
        display: inline-block;
    }
    .delete_icon{
        position: absolute;
        right:0;
        top:-10px;
        z-index: 1;
        cursor: pointer;
    }
</style>
