<template>
    <a-modal
      :title="title"
      :width="1100"
      :visible="visible"
      :confirmLoading="confirmLoading"
      @cancel="handleCancel"
      cancelText="关闭">

      <a-spin :spinning="confirmLoading">
        <a-card :bordered="false" :body-style="{padding: '0'}">
          <div class="salesCard">
            <a-tabs default-active-key="1" size="large" :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}">
              <a-tab-pane loading="true" table="图表" key="1">
                <div class="table-page-search-wrapper">
                  <a-form layout="inline" @keyup.enter.native="searchQuery">
                    <a-row :gutter="24">
                      <a-col :md="10" :sm="14">
                        <a-form-item>
                          <j-date v-model="createTime_begin" date-format="YYYY-MM-DD 00:00:00" class="query-group-cust" placeholder="请选择充值开始时间" @click="searchQuery"/>
                          <span style="width: 10px;"> ~ </span>
                          <j-date v-model="createTime_end" dete-format="YYYY-MM-DD 00:00:00" class="query-group-cust" placeholder="请选择充值结束时间" @click="searchQuery"/>
                        </a-form-item>
                      </a-col>
                      <a-col :md="4" :sm="6">
                        <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                          <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
                        </span>
                      </a-col>
                    </a-row>
                  </a-form>
                </div>
                <line-chart-multid :fields="['num']" :dataSource="lineData" :aliases="[{field:'num',alias:'金额'}]" title="充值记录"/>
                <div>
                  总充值金额: {{moneyCount}}
                </div>
              </a-tab-pane>
            </a-tabs>

          </div>
        </a-card>
      </a-spin>
    </a-modal>
</template>

<script>
    import JDate from "../../../../components/jeecg/JDate";
    import LineChartMultid from "../../../../components/chart/LineChartMultid";
    import {getAction} from "../../../../api/manage";
    export default {
        name: "RechargeOrderReportModal",
      components: {LineChartMultid, JDate},
      data () {
          return {
            title:"操作",
            visible: false,
            createTime_begin: '',
            createTime_end: '',
            confirmLoading:false,
            lineData: [],
            moneyCount: '',
            url: {
              queryMoneyChartUrl: "/order/iotRechargeOrder/queryMoneyData"
            }
          }
        },
        created() {

        },
        methods: {
          searchQuery() {
            let createTimeBegin = this.createTime_begin
            let createTimeEnd = this.createTime_end
            this.initLineData(createTimeBegin,createTimeEnd);
          },
          initLineData(createTimeBegin,createTimeEnd){
            this.lineData = [];
            let params = {createTimeBegin: createTimeBegin,createTimeEnd:createTimeEnd};
            getAction(this.url.queryMoneyChartUrl,params).then((res) => {
              if (res.success) {
                this.lineData = res.result.lineData;
                this.moneyCount = res.result.moneyCount;
              }
            })

          },
          see() {
            this.visible = true
            this.initLineData("","");
          },
          close () {

            this.$emit('close');
            this.visible = false;
          },
          handleCancel() {
            this.close()
          },
          handleOk() {
            this.$emit('close')
          }
        }
    }
</script>

<style scoped>

</style>