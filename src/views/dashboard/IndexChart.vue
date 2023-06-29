<template>
  <div class="page-header-index-wide">

    <a-row :gutter="24" v-has="'iot:admin'">
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="总充值金额（元）" :total="oneRow.sumMoney">
          <a-tooltip title="订单金额情况" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div v-for="(item,i) in oneRow.allMchs">
            <trend  style="margin-right: 16px;">
              <span slot="term">{{item.mchName}}: </span>
              {{item.tradingMoney}}
            </trend>
          </div>
          <template slot="footer">日均销售额<span>￥ 234.56</span></template>
        </chart-card>
      </a-col>

      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="总支付笔数" :total="oneRow.sumCount ">
          <a-tooltip title="订单数量情况" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div>
            <!--<mini-bar :height="40" />-->
            <div v-for="(item,i) in oneRow.allMchsCount">
              <trend  style="margin-right: 16px;">
                <span slot="term">{{item.mchName}}: </span>
                {{item.count}}
              </trend>
            </div>
          </div>
          <template slot="footer">转化率 <span>60%</span></template>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="电信卡数量" :total="oneRow.telecomCardSumCount">
          <a-tooltip title="电信卡信息" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div>
            <!--<mini-area />-->
            <div>
              <span slot="term">待激活：</span>
              {{oneRow.telecomSilentCount}}
            </div>
            <div>
              <span slot="term">正常：</span>
              {{oneRow.telecomActiveCount}}
            </div>
            <div>
              <span slot="term">停机：</span>
              {{oneRow.telecomStopCount}}
            </div>
          </div>
          <template slot="footer">日访问量<span> {{ '1234' | NumberFormat }}</span></template>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="移动卡数量" :total="oneRow.cardSumCount | NumberFormat">
          <a-tooltip title="移动卡信息" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div>
            <!--<mini-progress color="rgb(19, 194, 194)" :target="80" :percentage="78" :height="8" />-->
              <span slot="term">沉默期：</span>
              {{oneRow.silentCount}}
          </div>
          <div>
              <span slot="term">正常：</span>
              {{oneRow.activeCount}}
          </div>
          <div>
              <span slot="term">停机：</span>
              {{oneRow.stopCount}}
          </div>
        </chart-card>
      </a-col>
    </a-row>

    <a-row v-has="'iot:ranking'">
      <a-col :span="24">
        <a-card :loading="loading" :bordered="false" :body-style="{padding: '0'}">
          <div class="salesCard">
            <a-tabs default-active-key="1" size="large" :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}">
              <!--<div class="extra-wrapper" slot="tabBarExtraContent">
                <a-month-picker placeholder="请选择月份" @change="onChange" />
              </div>-->
              <a-tab-pane loading="true" tab="排行榜" key="1">
                <a-row>
                  <!--<a-col :xl="6" :lg="12" :md="12" :sm="24" :xs="24">-->
                    <!--<rank-list title="推广员推广排行榜" :list="promoteList"/>-->
                  <!--</a-col>-->
                  <!--<a-col :xl="6" :lg="12" :md="12" :sm="24" :xs="24">-->
                    <!--<rank-list title="店铺开卡排行榜" :list="rankList"/>-->
                  <!--</a-col>-->
                  <a-col :xl="12" :lg="12" :md="12" :sm="24" :xs="24">
                    <rank-list title="卡用量排行榜（移动）" :list="mobileFlowList"/>
                  </a-col>
                  <a-col :xl="12" :lg="12" :md="12" :sm="24" :xs="24">
                    <rank-list title="卡用量排行榜（电信）" :list="flowList"/>
                  </a-col>
                </a-row>
              </a-tab-pane>
            </a-tabs>
          </div>
        </a-card>
      </a-col>
    </a-row>

    <a-card :loading="loading" :bordered="false" :body-style="{padding: '0'}" :style="{ marginTop: '24px' }">
      <div class="salesCard">
        <a-tabs default-active-key="1" size="large" :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}">
          <div class="extra-wrapper" slot="tabBarExtraContent">
            <!--<div class="extra-item">
            <a-select allowClear="true" placeholder="请选择运营商" style="width: 120px" @change="handleChange">
              <a-select-option value="1">移动</a-select-option>
              <a-select-option value="2">联通</a-select-option>
              <a-select-option value="3">电信</a-select-option>
              <a-select-option value="4">第三方</a-select-option>
            </a-select>
            </div>-->
            <a-month-picker placeholder="请选择月份" @change="onChange" />
          </div>
          <a-tab-pane loading="true" tab="销售额" key="1">
            <a-row>
              <a-col :xl="24" :lg="12" :md="12" :sm="24" :xs="24">
                <bar title="销售额额排行(当月)" :dataSource="barData"/>
              </a-col>
            </a-row>
          </a-tab-pane>
        </a-tabs>
      </div>
    </a-card>

<!--    <a-card :loading="loading" :bordered="false" :body-style="{padding: '0'}" :style="{ marginTop: '24px' }">-->
<!--      <div class="salesCard">-->
<!--        <a-tabs default-active-key="1" size="large" :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}">-->
<!--          <div class="extra-wrapper" slot="tabBarExtraContent">-->
<!--            <div class="extra-item">-->
<!--              <a-select allowClear="true" placeholder="请选择运营商" style="width: 120px" @change="handleChange">-->
<!--                <a-select-option value="1">移动</a-select-option>-->
<!--                <a-select-option value="2">联通</a-select-option>-->
<!--                <a-select-option value="3">电信</a-select-option>-->
<!--              </a-select>-->
<!--            </div>-->
<!--          </div>-->
<!--          <a-tab-pane loading="true" tab="开卡量" key="1">-->
<!--            <a-row>-->
<!--              <a-col :span="6">-->
<!--                <head-info title="今日开卡量" :content="loginfo.todayCount"></head-info>-->
<!--              </a-col>-->
<!--              <a-col :span="2">-->
<!--                <a-spin class='circle-cust'>-->
<!--                  <a-icon slot="indicator" type="environment" style="font-size: 24px"  />-->
<!--                </a-spin>-->
<!--              </a-col>-->
<!--              <a-col :span="6">-->
<!--                <head-info title="昨日开卡量" :content="loginfo.yesterdayCount"></head-info>-->
<!--              </a-col>-->
<!--              <a-col :span="2">-->
<!--                <a-spin class='circle-cust'>-->
<!--                  <a-icon slot="indicator" type="team" style="font-size: 24px"  />-->
<!--                </a-spin>-->
<!--              </a-col>-->
<!--              <a-col :span="6">-->
<!--                <head-info title="近7天总开卡量" :content="loginfo.totalCount"></head-info>-->
<!--              </a-col>-->
<!--              <a-col :span="2">-->
<!--                <a-spin class='circle-cust'>-->
<!--                  <a-icon slot="indicator" type="rise" style="font-size: 24px"  />-->
<!--                </a-spin>-->
<!--              </a-col>-->
<!--            </a-row>-->
<!--            <line-chart-multid :fields="visitFields" :dataSource="visitInfo"></line-chart-multid>-->
<!--          </a-tab-pane>-->
<!--        </a-tabs>-->
<!--      </div>-->
<!--    </a-card>-->
    <!--<a-row v-has="'iot:admin'">
      <a-col :span="24">
        <a-card :loading="loading" :bordered="false" title="开卡量" :style="{ marginTop: '24px' }">
          <a-row>
            <a-col :span="6">
              <head-info title="今日开卡量" :content="loginfo.todayCount"></head-info>
            </a-col>
            <a-col :span="2">
              <a-spin class='circle-cust'>
                <a-icon slot="indicator" type="environment" style="font-size: 24px"  />
              </a-spin>
            </a-col>
            <a-col :span="6">
              <head-info title="昨日开卡量" :content="loginfo.yesterdayCount"></head-info>
            </a-col>
            <a-col :span="2">
              <a-spin class='circle-cust'>
                <a-icon slot="indicator" type="team" style="font-size: 24px"  />
              </a-spin>
            </a-col>
            <a-col :span="6">
              <head-info title="近7天总开卡量" :content="loginfo.totalCount"></head-info>
            </a-col>
            <a-col :span="2">
              <a-spin class='circle-cust'>
                <a-icon slot="indicator" type="rise" style="font-size: 24px"  />
              </a-spin>
            </a-col>
          </a-row>
          <line-chart-multid :fields="visitFields" :dataSource="visitInfo"></line-chart-multid>
        </a-card>
      </a-col>
    </a-row>-->
  </div>
</template>

<script>
  import ChartCard from '@/components/ChartCard'
  import ACol from "ant-design-vue/es/grid/Col"
  import ATooltip from "ant-design-vue/es/tooltip/Tooltip"
  import MiniArea from '@/components/chart/MiniArea'
  import MiniBar from '@/components/chart/MiniBar'
  import MiniProgress from '@/components/chart/MiniProgress'
  import RankList from '@/components/chart/RankList'
  import Bar from '@/components/chart/Bar'
  import LineChartMultid from '@/components/chart/LineChartMultid'
  import HeadInfo from '@/components/tools/HeadInfo.vue'
  import Trend from '@/components/Trend'
  import { getLoginfo,getVisitInfo } from '@/api/api'
  import { getAction,deleteAction,putAction,postAction} from '@/api/manage'
  import JDate from '@/components/jeecg/JDate'

  export default {
    name: "IndexChart",
    components: {
      ATooltip,
      ACol,
      ChartCard,
      MiniArea,
      MiniBar,
      MiniProgress,
      RankList,
      Bar,
      Trend,
      LineChartMultid,
      HeadInfo,
      JDate
    },
    data() {
      return {
        oneRow:{
          //销售额统计
          sumMoney:'',
          allMchs:[],
          //支付统计
          sumCount:'',
          allMchsCount:[],
          //移动卡数量统计：
          cardSumCount:'',
          silentCount:'',
          activeCount:'',
          stopCount:'',
          //电信卡数量统计
          telecomCardSumCount:'',
          telecomSilentCount:'',
          telecomActiveCount:'',
          telecomStopCount:'',

        },
        monthDate:'',
        operatorType:'',
        jdate:'',
        loading: true,
        center: null,
        rankList:[],
        promoteList:[],
        flowList:[],
        mobileFlowList:[],
        barData:[],
        loginfo:{},
        visitFields:['数量'],
        visitInfo:[],
        indicator: <a-icon type="loading" style="font-size: 24px" spin />,
        url: {
          getBarDataUrl: "/order/order/barDatalist",
          getRankListUrl:"/telecomcardinformation/telecomCardInformation/rankList",
          getFlowListUrl:"/telecomcardinformation/telecomCardInformation/flowList",
          getMobileFlowListUrl:"/cardinformation/cardInformation/flowList",
          getPromoteListUrl:"/sys/user/promoteList",
          getCardNumUrl:"/telecomcardinformation/telecomCardInformation/activedCount",
          getCardNumByOperatorUrl:"/telecomcardinformation/telecomCardInformation/totalCount"
        }
      }
    },
    created() {
      setTimeout(() => {
        this.loading = !this.loading
      }, 1000)
      this.initLogInfo(this.monthDate,this.operatorType);
      this.getOneRowData();
      /*let dataString="";
      let operatorType="";*/
      this.initBarData(this.monthDate,this.operatorType);
      this.initRankList(this.monthDate,this.operatorType);
      this.initPromoteList(this.monthDate,this.operatorType);
      this.initFlowList(this.monthDate,this.operatorType);
      this.initMobileFlowList(this.monthDate,this.operatorType);
    },
    methods: {
      getOneRowData(){
        getAction("dashboard/oneRowData").then((res)=>{
          if(res.success){
            console.log(res.result)
            //交易额
            this.oneRow.sumMoney = res.result.sumMoney;
            this.oneRow.allMchs = res.result.allMchs;
            //支付笔数
            this.oneRow.allMchsCount = res.result.allMchsCount;
            this.oneRow.sumCount = res.result.sumCount;
            //卡数量
            this.oneRow.cardSumCount = res.result.cardSumCount;
            this.oneRow.silentCount = res.result.silentCount;
            this.oneRow.activeCount = res.result.activeCount;
            this.oneRow.stopCount = res.result.stopCount;
            //电信卡
            this.oneRow.telecomCardSumCount = res.result.telecomCardSumCount;
            this.oneRow.telecomSilentCount = res.result.telecomSilentCount;
            this.oneRow.telecomActiveCount = res.result.telecomActiveCount;
            this.oneRow.telecomStopCount = res.result.telecomStopCount;
          }
        })
      },
      initLogInfo (dataString,operatorType) {
        let params={dataString:dataString,operatorType:operatorType};
        getAction(this.url.getCardNumUrl,params).then((res)=>{
          if(res.success){
            console.log("bbbbbb",res.result)
            Object.keys(res.result).forEach(key=>{
              res.result[key] =res.result[key]+""
            })
            this.loginfo = res.result;
          }
        })
        getAction(this.url.getCardNumByOperatorUrl,params).then((res)=>{
          if(res.success){
            console.log("aaaaaa",res.result)
            this.visitInfo = res.result;
          }
        })
      },
      initBarData(dataString,operatorType){
        let params={dataString:dataString,operatorType:operatorType};
        getAction(this.url.getBarDataUrl,params).then((res)=>{
          if(res.success){
            console.log(res.result)
            for (let i = 0; i < res.result.length; i++) {
              this.barData.push({
                x: res.result[i].transfer+'',
                y: res.result[i].value
              })
            }
          }
        })

      },
      initRankList(dataString,operatorType){
        let params={dataString:dataString,operatorType:operatorType};
        getAction(this.url.getRankListUrl,params).then((res)=>{
          if(res.success){
            console.log(res.result)
            for (let i = 0; i < res.result.length; i++) {
              this.rankList.push({
                name: res.result[i].transfer,
                total:res.result[i].value+'张'
              })
            }
          }
        })

      },
      initPromoteList(dataString,operatorType){
        let params={dataString:dataString,operatorType:operatorType};
        getAction(this.url.getPromoteListUrl,params).then((res)=>{
          if(res.success){
            for (let i = 0; i < res.result.length; i++) {
              this.promoteList.push({
                name: res.result[i].transfer,
                total:res.result[i].value+'个'
              })
            }
          }
        })
      },
      initFlowList(dataString,operatorType){
        let params={dataString:dataString,operatorType:operatorType};
        getAction(this.url.getFlowListUrl,params).then((res)=>{
          if(res.success){
            for (let i = 0; i < res.result.length; i++) {
              this.flowList.push({
                name: res.result[i].transfer,
                total:res.result[i].value+'M'
              })
            }
          }
        })
      },
      initMobileFlowList(dataString,operatorType){
        let params={dataString:dataString,operatorType:operatorType};
        getAction(this.url.getMobileFlowListUrl,params).then((res)=>{
          if(res.success){
            for (let i = 0; i < res.result.length; i++) {
              this.mobileFlowList.push({
                name: res.result[i].transfer,
                total:res.result[i].value+'M'
              })
            }
          }
        })
      },
      onChange(date, dateString) {
        this.barData=[];
        this.rankList=[];
        this.promoteList=[];
        this.flowList=[];
        this.mobileFlowList=[];
        this.loginfo={};
        this.monthDate=dateString;
        this.initBarData(this.monthDate,this.operatorType);
        this.initRankList(this.monthDate,this.operatorType);
        this.initPromoteList(this.monthDate,this.operatorType);
        this.initFlowList(this.monthDate,this.operatorType);
        this.initLogInfo(this.monthDate,this.operatorType);
        this.initMobileFlowList(this.monthDate,this.operatorType);
        },handleChange(value) {
        console.log(`selected ${value}`);
        this.barData=[];
        this.rankList=[];
        this.promoteList=[];
        this.flowList=[];
        this.mobileFlowList=[];
        this.loginfo={};
        this.operatorType=value;
        this.initBarData(this.monthDate,this.operatorType);
        this.initRankList(this.monthDate,this.operatorType);
        this.initPromoteList(this.monthDate,this.operatorType);
        this.initFlowList(this.monthDate,this.operatorType);
        this.initLogInfo(this.monthDate,this.operatorType);
        this.initMobileFlowList(this.monthDate,this.operatorType);
      },
    }
  }
</script>

<style lang="scss" scoped>
  .circle-cust{
    position: relative;
    top: 28px;
    left: -100%;
  }
  .extra-wrapper {
    line-height: 55px;
    padding-right: 24px;

    .extra-item {
      display: inline-block;
      margin-right: 24px;

      a {
        margin-left: 24px;
      }
    }
  }

  /* 首页访问量统计 */
  .head-info {
    position: relative;
    text-align: left;
    padding: 0 32px 0 0;
    min-width: 125px;

    &.center {
      text-align: center;
      padding: 0 32px;
    }

    span {
      color: rgba(0, 0, 0, .45);
      display: inline-block;
      font-size: .95rem;
      line-height: 42px;
      margin-bottom: 4px;
    }
    p {
      line-height: 42px;
      margin: 0;
      a {
        font-weight: 600;
        font-size: 1rem;
      }
    }
  }
</style>