<template>
  <a-modal
    :title="title"
    :width="1100"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-spin :spinning="confirmLoading">
      <a-card :bordered="false" :body-style="{padding: '0'}">
        <div class="salesCard">

          <a-tabs default-active-key="1" size="large" :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}">
            <!--<div class="extra-wrapper" slot="tabBarExtraContent" style="width: 350px">-->
              <!--&lt;!&ndash;<j-tree-select-->
                <!--@change="handleChange"-->
                <!--v-model="cusId"-->
                <!--placeholder="请选择客户"-->
                <!--dict="sys_user,user_company,id"-->
                <!--condition='{"user_type":"4"}'-->
                <!--pidField="create_by_id"-->
                <!--pidValue="ae95dbad4e2e444bb6a1fce9582aac5f"/>&ndash;&gt;-->


              <!--&lt;!&ndash;<a-button type="primary" @click="searchQuery" icon="search">查询</a-button>&ndash;&gt;-->
            <!--</div>-->

            <a-tab-pane loading="true" tab="报表" key="1">
              <div class="table-page-search-wrapper">
                <a-form layout="inline" @keyup.enter.native="searchQuery">
                  <a-row :gutter="24">
                    <a-col :md="10" :sm="14">
                      <a-form-item label="收单日期">
                        <j-date v-model="createTime_begin"  date-format="YYYY-MM-DD 00:00:00" class="query-group-cust" placeholder="开始时间" ></j-date>
                        <span style="width: 10px;"> ~ </span>
                        <j-date v-model="createTime_end"  date-format="YYYY-MM-DD 23:59:59" class="query-group-cust" placeholder="结束时间" ></j-date>
                      </a-form-item>

                      <a-form-item label="提单日期">
                        <j-date v-model="commitTime_begin"  date-format="YYYY-MM-DD 00:00:00" class="query-group-cust" placeholder="开始时间" ></j-date>
                        <span style="width: 10px;"> ~ </span>
                        <j-date v-model="commitTime_end"  date-format="YYYY-MM-DD 23:59:59" class="query-group-cust" placeholder="结束时间" ></j-date>
                      </a-form-item>

                    </a-col>
                    <a-col :md="8" :sm="12">
                      <a-form-item label="代理商">
                        <a-select
                          allowClear="true"
                          v-model="selectValue"
                          mode="multiple"
                          style="width: 100%"
                          placeholder="请选择"
                          :options="dictOptions"
                          :filterOption="likeQuery"
                          :autoClearSearchValue="false"
                          :maxTagTextLength=3
                          :maxTagCount=3
                        ></a-select>
                      </a-form-item>
                    </a-col>
                    <a-col :md="8" :sm="12">
                      <a-form-item v-show="show1" label="运营商">
                        <a-select
                          allowClear="true"
                          v-model="operatorValue"
                          mode="multiple"
                          style="width: 100%"
                          placeholder="请选择"
                          :options="dictOperatorOptions"
                          :filterOption="likeQuery"
                          :autoClearSearchValue="false"
                          :maxTagTextLength=3
                          :maxTagCount=3
                        ></a-select>
                      </a-form-item>
                      <a-col :md="20" :sm="12">
                        <a-form-item v-show="show2" label="运营商" >
                          <a-select
                            v-model="allAgentOperatorValue"
                            allowClear="true"
                            mode="multiple"
                            style="width: 100%"
                            placeholder="请选择"
                            :options="allAgentDictOperatorOptions"
                            :filterOption="likeQuery"
                            :autoClearSearchValue="false"
                            :maxTagTextLength=3
                            :maxTagCount=3
                          ></a-select>
                        </a-form-item>
                      </a-col>
                    </a-col>
                    <a-col :md="4" :sm="6" >
                      <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                        <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
                      </span>
                    </a-col>
                  </a-row>
                </a-form>
              </div>

              <a-table
                ref="table"
                :scroll="scrollTrigger"
                size="middle"
                rowKey="id"
                :columns="columns"
                :dataSource="dataSource">
              </a-table>
            </a-tab-pane>
            <a-tab-pane loading="true" tab="图表" key="2">
              <div class="table-page-search-wrapper">
                <a-form layout="inline" @keyup.enter.native="searchQuery2">
                  <a-row :gutter="24">
                    <a-col :md="10" :sm="14">
                      <a-form-item>
                        <j-date v-model="createTime_begin2"  date-format="YYYY-MM-DD 00:00:00" class="query-group-cust" placeholder="请选择激活开始时间" @click="searchQuery2"/>
                        <span style="width: 10px;"> ~ </span>
                        <j-date v-model="createTime_end2"  date-format="YYYY-MM-DD 23:59:59" class="query-group-cust" placeholder="请选择激活结束时间" @click="searchQuery2"/>
                      </a-form-item>
                    </a-col>
                    <a-col :md="8" :sm="12">
                      <a-form-item label="代理商">
                        <a-select
                          allowClear="true"
                          v-model="selectValue2"
                          mode="multiple"
                          style="width: 100%"
                          placeholder="请选择"
                          :options="dictOptions"
                          :filterOption="likeQuery"
                          :autoClearSearchValue="false"
                          :maxTagTextLength=3
                          :maxTagCount=3
                        ></a-select>
                      </a-form-item>
                    </a-col>
                    <a-col :md="4" :sm="6" >
                      <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                        <a-button type="primary" @click="searchQuery2" icon="search">查询</a-button>
                      </span>
                    </a-col>
                  </a-row>
                </a-form>
              </div>
              <line-chart-multid :fields="['num']" :dataSource="lineData" :aliases="[{field:'num',alias:'数量'}]" title="卡激活" />
              <div>
                激活订单数：{{activeCount}}
              </div>
              <div class="table-page-search-wrapper">
                <a-form layout="inline" @keyup.enter.native="searchQuery3">
                  <a-row :gutter="24">
                    <a-col :md="10" :sm="14">
                      <a-form-item>
<!--                        <span style="width: 10px;"> 激活日期： </span>-->
                        <j-date v-model="createTime_begin3" date-format="YYYY-MM-DD" class="query-group-cust" placeholder="请选择激活时间"/>
                        <span style="width: 10px;"> ~ </span>
                        <j-date v-model="createTime_end3" date-format="YYYY-MM-DD" class="query-group-cust" placeholder="请选择激活时间"/>
                      </a-form-item>
                    </a-col>
                    <a-col :md="8" :sm="12">
                      <a-form-item label="代理商" >
                        <a-select
                          v-model="selectValue3"
                          allowClear="true"
                          mode="multiple"
                          style="width: 100%"
                          placeholder="请选择"
                          :options="dictOptions"
                          :filterOption="likeQuery"
                          :autoClearSearchValue="false"
                          :maxTagTextLength=3
                          :maxTagCount=3
                        ></a-select>
                      </a-form-item>
                    </a-col>
                    <a-col :md="4" :sm="6" >
                      <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                        <a-button type="primary" @click="searchQuery3" icon="search">查询</a-button>
                      </span>
                    </a-col>
                  </a-row>
                </a-form>
              </div>
              <line-chart-multid :fields="['num']" :dataSource="lineDataByDay" :aliases="[{field:'num',alias:'数量'}]" title="卡激活" />
              <div>
                激活订单数: {{activeCount1}}
              </div>
            </a-tab-pane>
          </a-tabs>
        </div>
      </a-card>
    </a-spin>
  </a-modal>
</template>

<script>
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import { getAction,deleteAction,putAction,postAction} from '@/api/manage'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import Bar from '@/components/chart/Bar'
  import JDate from '@/components/jeecg/JDate'
  import LineChartMultid from '@/components/chart/LineChartMultid'
  import JMultiSelectTag from '@/components/dict/JMultiSelectTag'
  export default {
    name: "ReportModal",
    components: {
      JTreeSelect,Bar,JDate,LineChartMultid,JMultiSelectTag,JSearchSelectTag
    },
      // props: {
      //     state: String,
      //     required: true
      // },
    data () {
      return {
        allAgentOperatorValue:[],
        allAgentDictOperatorOptions:[],
        show1:null,
        show2:null,
        state: '',
        selectValue:[],
        operatorValue: [],
        selectValue2:[],
        selectValue3:[],
        checkboxValue:"",
        dictOptions:[],
        dictOperatorOptions:[],
        scrollTrigger: {},
        ipagination: {
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + '-' + range[1] + ' 共' + total + '条'
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        columns: [
          {
            title: '代理商',
            align: 'center',
            dataIndex: 'realname'
          },
          {
            title: '订单总数',
            align: 'center',
            dataIndex: 'nums'
          },
          {
            title: '订单作废数',
            align: 'center',
            dataIndex: 'zf'
          },
          {
            title: '提交失败数',
            align: 'center',
            dataIndex: 'sb'
          },
          {
            title: '订单有效数',
            align: 'center',
            dataIndex: 'yx'
          },
            {
                title: '订单有效率',
                align: 'center',
                dataIndex: 'effective'
            },
          {
            title: '订单激活数',
            align: 'center',
            dataIndex: 'jh'
          },
          {
            title: '总激活率',
            align: 'center',
            dataIndex: 'sumJh'
          },
          {
            title: '发货激活率',
            align: 'center',
            dataIndex: 'rat'
          }
        ],
        dataSource: [],
        importBatch:'',
        errorMsg:'',
        title:"操作",
        visible: false,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        barData:[],
        createTime_begin:'',
        commitTime_begin:'',
        commitTime_end:'',
        createTime_end:'',
        createTime_begin2:'',
        createTime_begin3:'',
        createTime_end3:'',
        createTime_end2:'',
        lineData: [],
        lineDataByDay: [],
        allCount: 0,
        activeCount: 0,
        activeCount1: 0,
        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
        },
        url: {
          initAgentUrl: "/sys/user/initAgent",
          initOperatorUrl: "/electronchannelagent/electronChannelAgent/initOperator",
          queryPageReportListUrl: "/electronregular/electronChannelOrderRegular/queryPageReportList",
          queryActiveChartUrl: "/electronregular/electronChannelOrderRegular/queryActiveChartData",
          queryActiveChartByDayUrl: "/electronregular/electronChannelOrderRegular/queryActiveChartDataByDay",
          allAgentInitOperatorUrl:"/electronchannelagent/electronChannelAgent/initOperatorSimpleName"
        },
      }
    },
    created () {
      this.selectValue =[];
      this.operatorValue =[];
      this.initAgent();
      this.allAgentInitOperator();
      this.allAgentOperatorValue = []
    },
    methods: {
      allAgentInitOperator(){
        getAction(this.url.allAgentInitOperatorUrl).then((res)=>{
          if(res.success){
            this.allAgentDictOperatorOptions = res.result;
          }
        })

      },
      likeQuery(input, option) {
        return ( option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0 );
      },
      see (state) {
        debugger
        if ("all" === state) {
          this.show1 = false
          this.show2 = true
        } else {
          this.show1 = true
          this.show2 = false
        }
        this.state = state;
        this.initOperator();
        this.visible = true;
        this.initBarData("","");
        this.initLineData("","");
        this.initLineDataByDay("");
      },
      searchQuery(){
        let createTimeBegin=this.createTime_begin;
        let createTimeEnd=this.createTime_end;
        let commitTimeBegin = this.commitTime_begin;
        let commitTimeEnd = this.commitTime_end;
        this.initBarData(createTimeBegin,createTimeEnd,commitTimeBegin,commitTimeEnd)
      },
      searchQuery2(){
        let createTimeBegin=this.createTime_begin2;
        let createTimeEnd=this.createTime_end2;
        this.initLineData(createTimeBegin,createTimeEnd);
      },
      searchQuery3(){
        let createTimeBegin=this.createTime_begin3;
        let createTimeEnd=this.createTime_end3;
        this.initLineDataByDay(createTimeBegin,createTimeEnd);
        },
      close () {
        this.selectValue2 = []
        this.createTime_begin = "";
        this.createTime_end = "";
        this.selectValue = [];
        this.createTime_begin2 = "";
        this.createTime_end2 = "";
        this.selectValue2 = [];
        this.createTime_begin3 = "";
        this.selectValue3 = [];
        this.$emit('close');
        this.visible = false;
        this.createTime_begin3 = "";
        this.createTime_end3="";
        this.show1=null
        this.show2=null
        this.allAgentOperatorValue = []
      },
      handleOk () {
        this.$emit('close');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      },
      handleChange(value) {
        console.log(`selected ${value}`);
        this.initBarData(createTimeBegin,createTimeEnd);
      },
      initAgent(selectValue){
        let params={selectValue:selectValue};
        getAction(this.url.initAgentUrl,params).then((res)=>{
          if(res.success){
            this.dictOptions = res.result;
          }
        })

      },
        initOperator(){
          let params = {state:this.state}
            getAction(this.url.initOperatorUrl,params).then((res)=>{
                if(res.success){
                    this.dictOperatorOptions = res.result;
                }
            })

        },
      initBarData(createTimeBegin,createTimeEnd,commitTimeBegin,commitTimeEnd){
        let userIds = this.selectValue.join(",");
        let agentIds;
        if (this.state == "all") {
          agentIds = this.allAgentOperatorValue.join(",")
        } else {
          agentIds = this.operatorValue.join(",");
        }
        this.barData=[];
        let params={createTimeBegin:createTimeBegin,createTimeEnd:createTimeEnd,commitTimeBegin:commitTimeBegin,commitTimeEnd:commitTimeEnd,userIds:userIds,agentIds:agentIds,state: this.state};
        getAction(this.url.queryPageReportListUrl,params).then((res)=>{
          if(res.success){
            console.log(res.result)
            this.dataSource = res.result.records;
          }
        })

      },
      initLineData(createTimeBegin,createTimeEnd){
        this.lineData = [];
        let userIds = this.selectValue2.join(",");
        let params={createTimeBegin: createTimeBegin,createTimeEnd: createTimeEnd,userIds:userIds,flag:this.state};
        getAction(this.url.queryActiveChartUrl,params).then((res)=>{
          if(res.success){
            this.lineData = res.result.lineData;
            this.allCount = res.result.allCount;
            this.activeCount = res.result.activeCount;
          }
        })

      },
        initLineDataByDay(createTimeBegin,createTimeEnd){
            this.lineDataByDay = [];
            let userIds = this.selectValue3.join(",");
            let params={createTimeBegin: createTimeBegin,createTimeEnd: createTimeEnd,userIds:userIds,flag:this.state};
            getAction(this.url.queryActiveChartByDayUrl,params).then((res)=>{
                if(res.success){
                    this.lineDataByDay = res.result.lineDataByDay;
                    this.activeCount1 = res.result.activeCount;
                }
            })

        }

    }
  }
</script>

<style lang="less" scoped>

</style>