<template>
    <div class="invoice">
        <div class="invoice-company">
            <span class="pull-right hidden-print">
                <a href="javascript:history.back();" class="btn btn-danger"><i class="fa fa-close"></i> 返回</a>
                <a href="javascript:;" onclick="window.print()" class="btn btn-success"><i class="fa fa-print"></i> 打印</a>
            </span>
        </div>
        <br/>

        <div style="width: 100%; display: flex; flex-direction: row; justify-content: center">
            <h4>记账凭证</h4>
        </div>

        <table class="table table-bordered">
            <thead>
            <tr>
                <th>NO.</th>
                <th>摘要</th>
                <th>总账科目</th>
                <th>明细科目</th>
                <th>借方金额</th>
                <th>贷方金额</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="(item, index) in dataList">
                <td>{{index + 1}}</td>
                <td>{{showSummary(item)}}</td>
                <td>{{showTotalInfo(item)}}</td>
                <td>{{showDetailInfo(item)}}</td>
                <td style="text-align: right">{{item.store_price}}</td>
                <td style="text-align: right">{{item.loan_price}}</td>
            </tr>
            </tbody>
        </table>

        <div style="width: 100%; display: flex; flex-direction: row; justify-content: flex-end">
            <div style="font-size: 16px; color: #666666">合计: {{queryData.total_price}}</div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/erc/cashiermanage/ERCMaterielSRControl?method=';

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            pagePara: {},
            dataList: []
        }
    },
    name: 'ERCMaterielSRControl',
    methods: {
        async getPagePara() {
            try {
                let response = await this.$http.post(apiUrl + 'initMaterielData', {});
                this.pagePara = response.data.info;
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async getRecordingVoucherDetail() {
            try {
                let response = await this.$http.post(apiUrl + 'getRecordingVoucherDetail', {...this.queryData});
                this.dataList = response.data.info.rows;
                this.dataList.push({
                    isTotal: true,
                    wms_type: parseInt(this.queryData.wms_type),
                    manage_type: parseInt(this.queryData.manage_type),
                    organization: this.queryData.organization,
                    org_type: this.queryData.org_type,
                    loan_price: this.queryData.total_price
                });
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        showSummary({wms_type, manage_type}) {
            if (wms_type === 1 && manage_type === 1) {
                return '采购入库单';
            } else if (wms_type === 1 && manage_type === 2) {
                return '产品入库单';
            } else if (wms_type === 1 && manage_type === 3) {
                return '其他入库单';
            } else if (wms_type === 2 && manage_type === 1) {
                return '销售出库单';
            } else if (wms_type === 2 && manage_type === 2) {
                return '生产领料单';
            } else if (wms_type === 2 && manage_type === 3) {
                return '其他出库单';
            }
        },
        showTotalInfo(data) {
            console.log('showTotalInfo:', data);
            const {isTotal, wms_type, manage_type, org_type, materiel_state_management} = data;
            if (isTotal) {
                if (wms_type === 1 && manage_type === 1) {
                    return '应付账款';
                } else if (wms_type === 1 && manage_type === 2) {
                    return '生产成本';
                } else if (wms_type === 1 && manage_type === 3) {
                    return '应付账款';
                } else if (wms_type === 2 && manage_type === 1) {
                    return '应收账款';
                } else if (wms_type === 2 && manage_type === 2) {
                    return '生产成本';
                } else if (wms_type === 2 && manage_type === 3) {
                    const [makeDept, saleDept, otherDept] = this.pagePara.departType;
                    switch (org_type) {
                        case makeDept.value:
                            return '制造费用';

                        case saleDept.value:
                            return '销售费用';

                        case otherDept.value:
                            return '管理费用';

                        default:
                            return '应收账款';
                    }
                }
            } else {
                if (wms_type === 1 && manage_type === 1) {
                    const result = this.pagePara.stateManagement.find(item => item.value === materiel_state_management);
                    return result.text;
                } else if (wms_type === 1 && manage_type === 2) {
                    const result = this.pagePara.stateManagement.find(item => item.value === materiel_state_management);
                    return result.text;
                } else if (wms_type === 1 && manage_type === 3) {
                    const result = this.pagePara.stateManagement.find(item => item.value === materiel_state_management);
                    return result.text;
                } else if (wms_type === 2 && manage_type === 1) {
                    const result = this.pagePara.stateManagement.find(item => item.value === materiel_state_management);
                    return result.text;
                } else if (wms_type === 2 && manage_type === 2) {
                    const result = this.pagePara.stateManagement.find(item => item.value === materiel_state_management);
                    return result.text;
                } else if (wms_type === 2 && manage_type === 3) {
                    const result = this.pagePara.stateManagement.find(item => item.value === materiel_state_management);
                    return result.text;
                }
            }
        },
        showDetailInfo({isTotal, wms_type, manage_type, materiel_name, materiel_format, organization, org_type}) {
            if (isTotal) {
                if (wms_type === 1 && manage_type === 1) {
                    return organization;
                } else if (wms_type === 1 && manage_type === 2) {
                    return `${organization}生产任务单`;
                } else if (wms_type === 1 && manage_type === 3) {
                    return organization;
                } else if (wms_type === 2 && manage_type === 1) {
                    return organization;
                } else if (wms_type === 2 && manage_type === 2) {
                    return `${organization}生产任务单`;
                } else if (wms_type === 2 && manage_type === 3) {
                    return '其他领用';
                }
            } else {
                if (wms_type === 1 && manage_type === 1) {
                    return `${materiel_name} ${materiel_format}`;
                } else if (wms_type === 1 && manage_type === 2) {
                    return `${materiel_name} ${materiel_format}`;
                } else if (wms_type === 1 && manage_type === 3) {
                    return `${materiel_name} ${materiel_format}`;
                } else if (wms_type === 2 && manage_type === 1) {
                    return `${materiel_name} ${materiel_format}`;
                } else if (wms_type === 2 && manage_type === 2) {
                    return `${materiel_name} ${materiel_format}`;
                } else if (wms_type === 2 && manage_type === 3) {
                    return `${materiel_name} ${materiel_format}`;
                }
            }
        },
        async onSearchBill() {
            console.log('onSearchNumber:', this.search_text);
            $('#dataTable').bootstrapTable("refresh");
        },
        async onGenerateBill() {
            this.bill_date = $('#bill_date').val();
            console.log('onGenerateBill:', this.bill_date);

            try {
                const result = await this.$http.post(apiUrl + 'genMaterielCollectionData', {bill_date: this.bill_date}).then(resp => resp.data);
                if (result.errno === 0) {
                    common.dealSuccessCommon('生成成功');
                    $('#dataTable').bootstrapTable("refresh");
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async initTable(table){
            window.tableEvents = {
                'click .show_detail': (e, value, row, index) => {
                    this.$router.push({
                        path: '/erc/cashiermanage/ERCMaterielSRDetailControl',
                        query: {
                            bill_date: row.bill_date,
                            organization: row.organization
                        }
                    })
                }
            };

            table.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getRecordingVoucherDetail',
                queryParams: (params) => {
                    params = this.queryData;
                    return JSON.stringify(params);
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('record_type', '摘要', (value, {wms_type, manage_type}) => {
                        if (wms_type === 1 && manage_type === 1) {
                            return `<div class="show_detail">采购入库单</div>`;
                        } else if (wms_type === 1 && manage_type === 2) {
                            return `<div class="show_detail">其他入库单</div>`;
                        } else if (wms_type === 2 && manage_type === 1) {
                            return `<div class="show_detail">销售出库单</div>`;
                        } else if (wms_type === 2 && manage_type === 2) {
                            return `<div class="show_detail">其他出库单</div>`;
                        }
                    }, tableEvents),
                    common.BTRowFormat('materiel_unit', '总账科目'),
                    common.BTRowFormat('materiel_name', '明细科目'),
                    common.BTRowFormatAlignRight('store_price', '借方金额'),
                    common.BTRowFormatAlignRight('paper_count', '单据张数'),
                ],
                idField: 'alldemand_id',
                uniqueId: 'alldemand_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row)
                },
            });
            common.changeTableClass(table);
        },
    },
    async mounted() {
        this.queryData = this.$route.query;
        await this.getPagePara();
        await this.getRecordingVoucherDetail();
    }
}
</script>
<style scoped>
    .invoice-bottom {
        font-size: 32px;
        color: #333333;
    }
</style>
