<template>
    <div class="invoice">
        <div class="invoice-company">
            <span class="pull-right hidden-print">
                <a href="javascript:history.back();" class="btn btn-danger"><i class="fa fa-close"></i> 返回</a>
                <a href="javascript:;" onclick="window.print()" class="btn btn-success"><i class="fa fa-print"></i> 打印</a>
            </span>
        </div>
        <br/>
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
                <td>{{item.digest}}</td>
                <td>{{item.accsum}}</td>
                <td>{{item.activeAccount}}</td>
                <td style="text-align: right">{{item.debite}}</td>
                <td style="text-align: right">{{item.credit}}</td>
            </tr>
            </tbody>
        </table>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/erc/cashiermanage/ERCRecordingVoucherSCControl?method=';

export default {
    data: function() {
        return {
            dataList: []
        }
    },
    name: 'ERCRecordingVoucherDetailSCControl',
    methods: {
        async getRecordingVoucherDetailC() {
            try {
                let response = await this.$http.post(apiUrl + 'getRecordingVoucherDetailC', {
                    cashiergatheringsum_time:this.queryData.recordingvouchersc_time,
                    department_id:this.queryData.recordingvouchersc_depart_id
                });
                this.dataList = response.data.info.rows;
                console.log(this.dataList)
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },

        async getRecordingVoucherDetailS() {
            try {
                let response = await this.$http.post(apiUrl + 'getRecordingVoucherDetailS', {
                    s_expense_confirm_time:this.queryData.recordingvouchersc_time,
                    department_id:this.queryData.recordingvouchersc_depart_id,
                    s_capital_cost_type:this.queryData.s_recordingvouchersc_type
                });
                this.dataList = response.data.info.rows;
                console.log(this.dataList)
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        }
    },
    async mounted() {
        this.queryData = this.$route.query;
        if(this.queryData.recordingvouchersc_type == 1){
            await this.getRecordingVoucherDetailC();
        }else {
            await this.getRecordingVoucherDetailS();
        }

    }
}
</script>
<style scoped>

</style>
