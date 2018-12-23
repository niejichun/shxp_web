<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/erc/homepage/ERCHomePageControl">首页</a></li>
        <li><a href="javascript:;">小程序基础信息维护</a></li>
        <li class="active">桌台类型设置</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">桌台类型列表</h4>
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
                    <table id="seatClassTable"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddModal" data-backdrop="static">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新建类别</h4>
                </div>
                <form @submit.prevent="addAct" id="formA">
                    <div class="modal-body row">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>名称</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.seatClass_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>标题</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.seatClass_title" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                        </div>

                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>预定单价</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.seatClass_price" maxlength="50" data-parsley-maxlength="50" onkeyup="this.value=this.value.replace(/\D/g,'')">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>总桌数</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.seatClass_sum" maxlength="50" data-parsley-maxlength="50" onkeyup="this.value=this.value.replace(/\D/g,'')">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-6">

                                <label class="col-sm-4 control-label"><span class="table-required">*</span>位置</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" id="seatClass_location" data-parsley-required="true"></select>
                                </div>

                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-xs-12">
                                <label>内容:</label>
                                <div id="textEditor"></div>
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
    const apiUrl = '/api/shxp/baseconfig/SHXPSeatClassControl?method=';
    var E = window.wangEditor;
    var editor = new E('#textEditor');
    export default {
        data: function() {
            return {
                workRow: {},
                oldRow: '',
                pagePara: {},
                files:[]

            }
        },
        name: 'SHXPSeatClassControl',
        mounted: async function() {
            let _self = this;
            _self.seatClassTable = $('#seatClassTable');
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
                        seatClass_img_url:file.file_url
                    };
                    _self.$http.post(apiUrl + 'removeFile', params).then(response =>{
                        if(response.body.errno==0){
                            this.seatClassTable.bootstrapTable("refresh");
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

                window.tableEvents = {
                    "click .delete" :async function (e, value, row, index){
                        let param = {
                            seatClass_id:row.seatClass_id,
                            seatClass_img_url:row.seatClass_img_url
                        }
                        let response = await _self.$http.post(apiUrl + 'deleteSeatClass', param);
                        common.dealSuccessCommon('删除成功');
                        _self.seatClassTable.bootstrapTable("refresh");

                    },
                }
                this.seatClassTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchSeatClass',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [

                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatEditable('seatClass_name', '类别名称'),
                        common.BTRowFormatEditable('seatClass_title', '标题'),
                        common.BTRowFormatEditable('seatClass_price', '预定单价'),
                        common.BTRowFormatWithFormatterAlignLeft('seatClass_remark', '描述',common.remarkFormatter),
                        common.BTRowFormatWithFormatter('seatClass_img_url', '图片', common.fileFormatter),
                        common.BTRowFormat('created_at', '创建日期'),
                        common.BTRowFormatEdSelect2(this, 'seatClass_location', '位置', 'shxpSeatLocation'),
                        common.actFormatter('act', ()=>{
                            return [
                                '<a class="btn btn-info btn-xs m-r-5 delete">删除</a>'
                            ].join('')
                        }, tableEvents)
                    ],
                    idField: 'seatClass_id',
                    uniqueId: 'seatClass_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    },
                    onEditableShown: (field, row, $el, editable) => {
                        this.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        common.rowModifyWithT(this, apiUrl + 'modifySeatClass', row, 'seatClass_id', this.masterTable)
                    }
                })
                common.changeTableClass(this.seatClassTable)
            },
            initData: async function () {
                editor.create();
                let response = await this.$http.post(apiUrl + 'init', {});
                this.pagePara = response.data.info;
                common.initSelect2($('#seatClass_location'), this.pagePara.shxpSeatLocation);
                $('#formA').parsley();
                this.initTable();
                common.reSizeCall();
            },
            addM: function(event) {
                this.workRow = {};
                this.files = []
                $('#designUpload').val('')
                $('#seatClass_location').val(null).trigger('change');
                $('#formA').parsley().reset();
                $('#AddModal').modal('show')
            },
            addAct: async function(event) {
                try {
                    if ($('#formA').parsley().isValid()) {
                        if(this.files && this.files.length>0){
                            this.workRow.seatClass_img_url = this.files[0].file_url;
                        }
                        this.workRow.seatClass_remark = editor.txt.html()
                        this.workRow.seatClass_location = common.getSelect2Val('seatClass_location');
                        let response = await this.$http.post(apiUrl + 'addSeatClass', this.workRow);
                        common.dealSuccessCommon('增加成功');
                        this.seatClassTable.bootstrapTable("refresh");
                        $('#AddModal').modal('hide')
                    }
                } catch (error) {
                    common.dealErrorCommon(this, error);
                }
            },
            queryConfirm: function(event) {
                this.seatClassTable.bootstrapTable("refresh")
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
