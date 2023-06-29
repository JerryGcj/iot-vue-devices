<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <a-col v-has="'cardInformationList:selectCustomerId'" :md="6" :sm="8">
            <a-form-item label="客户" >
              <!--<a-input placeholder="请输入客户" v-model="queryParam.userNameId"></a-input>-->
              <!--异步树加载组件 通过传入表名 显示字段 存储字段 加载一个树控件
              <j-tree-select dict="aa_tree_test,aad,id" pid-field="pid" ></j-tree-select>-->
              <j-tree-select
                v-model="queryParam.customerId"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                condition='{"del_flag":"0"}'
                pidField="create_by_id"
                pidValue=""/>
            </a-form-item>
          </a-col>

          <a-col v-has="'cardInformationList:selectCustomerIdDls'" :md="6" :sm="8">
            <a-form-item label="客户">
              <a-select v-model="queryParam.customerId"
                        placeholder="客户">
                <a-select-option v-for="d in userData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

          <a-col :md="8" :sm="10">
            <a-form-item label="ICCID">
              <a-input placeholder="开始ICCID" class="query-group-cust" v-model="queryParam.iccidStart"></a-input>
              <span class="query-group-split-cust"></span>
              <a-input placeholder="结束ICCID" class="query-group-cust" v-model="queryParam.iccidEnd"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="10">
            <a-form-item label="自定义ICCID">
              <a-input placeholder="开始ICCID" class="query-group-cust" v-model="queryParam.customIccidStart"></a-input>
              <span class="query-group-split-cust"></span>
              <a-input placeholder="结束ICCID" class="query-group-cust" v-model="queryParam.customIccidEnd"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="接入号">
              <a-input placeholder="接入号" v-model="queryParam.accessNumber"></a-input>
            </a-form-item>
          </a-col>
          <!--<a-col :md="4" :sm="6">
            <a-form-item label="运营商类型">
              <j-dict-select-tag placeholder="请选择运营商类型" v-model="queryParam.operatorType" dict-code="operator_type"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="运营商">
              <j-dict-select-tag v-model="queryParam.operatorId" placeholder="请选择运营商" dict-code="iot_operator,operator_name,id,state='1'"></j-dict-select-tag>
            </a-form-item>
          </a-col>-->

          <a-col :md="4" :sm="6">
            <a-form-item label="卡状态">
              <j-dict-select-tag v-model="queryParam.cardState" placeholder="请选择" dictCode="telecom_status"/>
            </a-form-item>
          </a-col>

          <a-col :md="10" :sm="12">
            <a-form-item label="激活时间" :labelCol="{span: 4}">
              <j-date v-model="queryParam.startTime" date-format="YYYY-MM-DD 00:00:00" style="width:45%" placeholder="请选择开始时间" ></j-date>
              <span class="query-group-split-cust"></span>
              <j-date v-model="queryParam.endTime" date-format="YYYY-MM-DD 23:59:59" style="width:45%" placeholder="请选择截止时间"></j-date>
            </a-form-item>
          </a-col>

          <template v-if="toggleSearchStatus">

          </template>
          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <!--<a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>-->
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>

    <!-- 操作按钮区域 -->
    <div class="table-operator">


      <a-button type="primary" v-has="'cardInformationList:importExcelUrl'" :action="importExcelUrl" icon="import" @click="handleCardImport">卡导入</a-button>
      <a-button type="primary" v-has="'cardInformationList:CardDistribution'" icon="" @click="handleCardDistribution"  @hello="onClearSelected">分配</a-button>
      <!--<a-button type="primary" v-has="'cardInformationList:CardImportDistribution'" icon="" @click="handleCardImportDistribution">导入分配</a-button>-->
      <a-button type="primary" v-has="'cardInformationList:CardStateChange'" icon="" @click="handleCardStateChange">状态变更</a-button>
      <a-button type="primary" v-has="'cardInformationList:DataUpdate'" icon="" @click="handleDataUpdate" :loading="confirmLoading">数据更新</a-button>
      <a-button type="primary" v-has="'cardInformationList:handleCorrection'" icon="" @click="handleCorrection">用量校正</a-button>
      <a-button type="primary" v-has="'cardInformationList:handleCardNote'" icon="" @click="handleCardNote">卡备注</a-button>
      <!--<a-button type="primary" v-has="'cardInformationList:CardSendSms'" icon="" @click="handleCardSendSms">发送短信</a-button>
      <a-button type="primary" v-has="'cardInformationList:CardImportSendSms'" icon="" @click="handleCardImportSendSms">按导入号码发送短信</a-button>
      &lt;!&ndash;<a-button type="primary" icon="" @click="handleCardRenewal">续费</a-button>&ndash;&gt;
      <a-button type="primary" v-has="'cardInformationList:CardImportSynchronousState'" icon="" @click="handleCardImportSynchronousState">按条件设置同步状态</a-button>
      <a-button type="primary" v-has="'cardInformationList:CardConditionalAgentRecovery'" icon="" @click="handleCardConditionalAgentRecovery">按条件代理不可复机</a-button>
      <a-button type="primary" v-has="'cardInformationList:CardDistributionPackage'" icon="" @click="handleCardDistributionPackage">设置套餐</a-button>-->
      <a-button type="primary" v-has="'cardInformationList:CardFlowUsageRatio'" icon="" @click="handleCardFlowUsageRatio">设置用量比例</a-button>
      <a-button type="primary" v-has="'cardInformationList:CardRecycling'" icon="" @click="handleCardInformationRecycling">卡回收</a-button>
      <!--<a-button type="primary" v-has="'cardInformationList:CardImportCardDistributionPackage'" icon="" @click="handleCardImportCardDistributionPackage">按导入卡号设置套餐</a-button>

      <a-button type="primary" v-has="'cardInformationList:CardAutomaticShutdown'" icon="" @click="handleCardAutomaticShutdown">设置自动停复机</a-button>
      <a-button type="primary" v-has="'cardInformationList:CardEditorInformation'" icon="" @click="handleCardEditorInformation">修改卡信息</a-button>
      <a-button type="primary" v-has="'cardInformationList:CardRealNameAuthentication'" icon="" @click="handleCardRealNameAuthentication">是否实名认证</a-button>

      <a-button type="primary" v-has="'cardInformationList:handleCardPriority'" icon="" @click="handleCardPriority">设置优先级</a-button>

      <a-button type="primary" v-has="'cardInformationList:handlePackageTransfer'"icon="" @click="handlePackageTransfer">流量套餐转移</a-button>-->
      <a-button type="primary" v-has="'cardInformationList:communicationPlan'" icon="" @click="speedLimitChange">设备限速</a-button>
      <a-button type="primary" v-has="'cardInformationList:CardDistributionPackage'" icon="" @click="handleCardDistributionPackage">设置套餐</a-button>
      <a-button type="primary" v-has="'cardInformationList:handleExportXls'"icon="download" @click="handleExportXls('流量卡管理信息表')">导出</a-button>

      <!--<a-dropdown v-if="selectedRowKeys.length > 0">-->
        <!--<a-menu slot="overlay">-->
          <!--<a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>-->
        <!--</a-menu>-->
        <!--<a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>-->
      <!--</a-dropdown>-->
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange"
        :scroll="{ x: 1800 }">

        <span slot="action" slot-scope="text, record">
          <a v-has="'cardInformationList:viewPackageList'" @click="handleDetails(record)">套餐列表</a>
          <a-divider v-has="'cardInformationList:vertical'" type="vertical" />
          <a v-has="'cardInformationList:dayUsed'" @click="cardUsage(record)">日用量<a-divider type="vertical" /></a>
          <a-dropdown>
            <a v-has="'cardInformationList:more'" class="ant-dropdown-link">
              更多 <a-icon type="down" />
            </a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a v-has="'cardInformationList:CardDistributionPackageOne'" @click="handleCardDistributionPackageOne(record)">套餐配置</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a v-has="'cardInformationList:CardDelete'">删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
          <!--<a v-has="'cardInformationList:viewPackageList'" @click="handleDetails(record)">套餐列表</a>
          <a-divider v-has="'cardInformationList:viewPackageList'" type="vertical" />
          <a v-has="'cardInformationList:CardDistributionPackageOne'" @click="handleCardDistributionPackageOne(record)">套餐配置</a>
          <a-divider type="vertical" />
          <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
             <a v-has="'cardInformationList:CardDelete'">删除</a>
          </a-popconfirm>-->

        </span>

      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <card-information-import ref="CardInformationImport" @ok="modalFormOk"></card-information-import>
    <card-information-distribution ref="CardInformationDistribution" @ok="modalFormOk"  @hello="onClearSelected"></card-information-distribution>
    <card-information-import-distribution ref="CardInformationImportDistribution" @ok="modalFormOk" ></card-information-import-distribution>
    <card-information-state-change ref="CardInformationStateChange" @ok="modalFormOk" @hello="onClearSelected"></card-information-state-change>
    <card-information-send-sms ref="CardInformationSendSms" @ok="modalFormOk"></card-information-send-sms>
    <card-information-import-send-sms ref="CardInformationImportSendSms" @ok="modalFormOk"></card-information-import-send-sms>
    <card-information-synchronous-state ref="CardInformationSynchronousState" @ok="modalFormOk"></card-information-synchronous-state>
    <card-information-conditional-agent-recovery ref="CardInformationConditionalAgentRecovery" @ok="modalFormOk"></card-information-conditional-agent-recovery>
    <card-information-flow-usage-ratio ref="CardInformationFlowUsageRatio" @ok="modalFormOk" @hello="onClearSelected"></card-information-flow-usage-ratio>
    <card-information-automatic-shutdown ref="CardInformationAutomaticShutdown" @ok="modalFormOk"></card-information-automatic-shutdown>

    <card-information-real-name-authentication ref="CardInformationRealNameAuthentication" @ok="modalFormOk"></card-information-real-name-authentication>
    <card-information-note ref="CardInformationNote" @ok="modalFormOk" @hello="onClearSelected"></card-information-note>

    <card-information-priority ref="CardInformationPriority" @ok="modalFormOk"></card-information-priority>
    <j-import-modal ref="importModal" :url="getImportUrl()" @ok="importOk"></j-import-modal>
    <card-information-distribution-package ref="CardInformationDistributionPackage" @ok="modalFormOk"></card-information-distribution-package>
    <card-information-rechargeable-package ref="CardInformationRechargeablePackage" @ok="modalFormOk"></card-information-rechargeable-package>
    <card-information-import-card-distribution-package ref="CardInformationImportCardDistributionPackage" @ok="modalFormOk"></card-information-import-card-distribution-package>
    <card-information-renewal ref="CardInformationRenewal" @ok="modalFormOk"></card-information-renewal>
    <card-information-editor-information ref="CardInformationEditorInformation" @ok="modalFormOk"></card-information-editor-information>
    <card-information-package-transfer ref="CardInformationPackageTransfer" @ok="modalFormOk"></card-information-package-transfer>
    <card-information-distribution-package-one ref="CardInformationDistributionPackageOne" @ok="modalFormOk"></card-information-distribution-package-one>

    <card-information-recycling ref="CardInformationRecycling" @ok="modalFormOk"></card-information-recycling>
    <card-information-speed-limit ref="CardInformationSpeedLimit" @ok="modalFormOk" @hello="onClearSelected"></card-information-speed-limit>
    <package-list-modal ref="packageListModal" @ok="modalFormOk"></package-list-modal>
    <card-usage-modal ref="CardUsageModal" @ok="modalFormOk"></card-usage-modal>
  </a-card>
</template>

<script>
  import CardInformationDistribution from './modules/CardInformationDistribution'
  import CardInformationImport from './modules/CardInformationImport'
  import CardInformationImportDistribution from './modules/CardInformationImportDistribution'
  import CardInformationStateChange from './modules/CardInformationStateChange'
  import CardInformationSendSms from './modules/CardInformationSendSms'
  import CardInformationImportSendSms from './modules/CardInformationImportSendSms'
  import CardInformationSynchronousState from './modules/CardInformationSynchronousState'
  import CardInformationConditionalAgentRecovery from './modules/CardInformationConditionalAgentRecovery'
  import CardInformationFlowUsageRatio from "./modules/CardInformationFlowUsageRatio";
  import CardInformationAutomaticShutdown from "./modules/CardInformationAutomaticShutdown";
  import CardInformationRealNameAuthentication from "./modules/CardInformationRealNameAuthentication";
  import CardInformationNote from "./modules/CardInformationNote";
  import CardInformationPriority from "./modules/CardInformationPriority";
  import CardInformationDistributionPackage from "./modules/CardInformationDistributionPackage";
  import CardInformationRechargeablePackage from "./modules/CardInformationRechargeablePackage";
  import CardInformationImportCardDistributionPackage from "./modules/CardInformationImportCardDistributionPackage";
  import CardInformationEditorInformation from "./modules/CardInformationEditorInformation";
  import CardInformationRenewal from "./modules/CardInformationRenewal";
  import CardInformationPackageTransfer from "./modules/CardInformationPackageTransfer";
  import CardInformationDistributionPackageOne from "./modules/CardInformationDistributionPackageOne";
  import PackageListModal from './modules/PackageListModal'
  import CardUsageModal from './modules/CardUsageModal'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import {queryOperator,queryStandardcost,telecomCardDataUpdate,getTelecomRefCardCostByCardId,queryLowerAgent} from '@/api/api'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import JDate from '@/components/jeecg/JDate'
  import { postAction,getAction,downFile} from '@/api/manage'
  import CardInformationRecycling from "./modules/CardInformationRecycling";
  import CardInformationSpeedLimit from "./modules/CardInformationSpeedLimit";


  export default {
    name: "CardInformationList",
    mixins:[JeecgListMixin],
    components: {
      PackageListModal,
      CardInformationRecycling,
      CardInformationRenewal,
      CardInformationEditorInformation,
      CardInformationRealNameAuthentication,
      CardInformationFlowUsageRatio,
      CardInformationImport,
      queryOperator,
      CardInformationDistribution,
      CardInformationImportDistribution,
      CardInformationStateChange,
      CardInformationSendSms,
      CardInformationImportSendSms,
      CardInformationSynchronousState,
      CardInformationConditionalAgentRecovery,
      CardInformationAutomaticShutdown,
      CardInformationNote,
      CardInformationPriority,
      CardInformationDistributionPackage,
      CardInformationRechargeablePackage,
      CardInformationImportCardDistributionPackage,
      JTreeSelect,
      queryStandardcost,
      JDate,
      CardInformationPackageTransfer,
      telecomCardDataUpdate,
      CardInformationDistributionPackageOne,
      getTelecomRefCardCostByCardId,
      queryLowerAgent,
      CardUsageModal,
      CardInformationSpeedLimit
    },
    data () {
      return {
        description: '流量卡管理信息表管理页面',
        operatorData:[],
        packageData:[],
        userData:[],
        username:'jeecg',
        confirmLoading: false,
        // fileList:[],
        // 表头
        columns: [
          /* {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
           }, */
		      {
            title: 'ICCID',
            align:"center",
            dataIndex: 'iccid',
            fixed:'left',
            width:200,
           },
		      {
            title: '接入号',
            align:"center",
            dataIndex: 'accessNumber', width: 180
           },
          {
            title: '自定义卡号',
            align:"center",
            dataIndex: 'virtualIccid',
            width: 150
          },
          /*{
            title: '运营商类型 ',
            align:"center",
            dataIndex: 'operatorType_dictText', width: 200
          },*/
          {
            title: '运营商',
            align:"center",
            dataIndex: 'operatorName',
            width: 200
          },

          // {
          //   title: '用户ID',
          //   align:"center",
          //   dataIndex: 'userName', width: 200
          // },
          {
            title: '企业名称',
            align:"center",
            dataIndex: 'userCompany', width: 200
          },
          {
            title: '状态',
            align:"center",
            dataIndex: 'simStatus_dictText',
            width: 150
          },
          {
            title: '主套餐',
            align:"center",
            dataIndex: 'takeEffectPackageName', width: 250
          },
          {
            title: '流量总量(M)',
            align:"center",
            dataIndex: 'originUse', width: 100
          },

          {
            title: '流量用量(M)',
            align:"center",
            dataIndex: 'usaged', width: 100
          },
          {
            title: '流量余量(M) ',
            align:"center",
            dataIndex: 'trafficAllowance', width: 100
          },
          /*{
            title: '资费用量(M) ',
            align:"center",
            dataIndex: 'monthToDateUsage', width: 100
          },*/
          {
            title: '激活时间',
            align:"center",
            dataIndex: 'dateActivated', width: 200
          },
          {
            title: '用量倍数',
            align:"center",
            dataIndex: 'customPackageUse', width: 80
          },
          {
            title: '速率',
            align:"center",
            dataIndex: 'speedValue_dictText', width: 80
          },
          {
            title: '备注 ',
            align:"center",
            dataIndex: 'note', width: 100
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
            width: 200
          }
        ],
		    url: {
          list: "/telecomcardinformation/telecomCardInformation/list",
          delete: "/telecomcardinformation/telecomCardInformation/delete",
          deleteBatch: "/telecomcardinformation/telecomCardInformation/deleteBatch",
          exportXlsUrl: "/telecomcardinformation/telecomCardInformation/exportXls",
          importExcelUrl: "/telecomcardinformation/telecomCardInformation/importExcel",
       },
    }
  },
  computed: {
    importExcelUrl: function(){

        var that = this;
      //   queryOperator().then((res)=>{
      //     if(res.success){
      //     that.operatorData = [];
      //     let treeList = res.result
      //     for(let a=0;a<treeList.length;a++){
      //       let temp = treeList[a];
      //       this.operatorData.push({
      //         value:temp.id,
      //         text:temp.operatorName
      //       })
      //     }
      //   }
      // });

     //  queryStandardcost().then((res)=>{
     //    if(res.success){
     //      that.packageData = [];
     //      let treeList = res.result
     //      for(let a=0;a<treeList.length;a++){
     //        let temp = treeList[a];
     //        this.packageData.push({
     //          value:temp.id,
     //          text:temp.packageName
     //        })
     //      }
     //    }
     // });


      var that = this;
      queryLowerAgent().then((res)=>{
        if(res.success){
        that.userData = [];
        let treeList = res.result
        for(let a=0;a<treeList.length;a++){
          let temp = treeList[a];
          this.userData.push({
            value:temp.id,
            text:temp.userCompany
          })
        }
      }
    });
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    },


  },
    methods: {
      cardUsage(record){
        this.$refs.CardUsageModal.edit(record.iccid);
        this.$refs.CardUsageModal.disableSubmit = true;
      },
      handleDetails(record){
        this.$refs.packageListModal.edit(record.iccid);
        this.$refs.packageListModal.disableSubmit = true;
      },
      //卡导入弹出页面
      handleCardImport: function () {

        this.$refs.CardInformationImport.add();
        this.$refs.CardInformationImport.title = "卡导入";
        this.$refs.CardInformationImport.disableSubmit = false;
      },

      //卡分导入配弹出页面
      handleCardImportDistribution: function () {

        this.$refs.CardInformationImportDistribution.add(this.selectedRowKeys.length);
        this.$refs.CardInformationImportDistribution.title = "卡导入分配";
        this.$refs.CardInformationImportDistribution.disableSubmit = false;
      },

      //发送短信弹出页面
      handleCardSendSms: function () {
        var params = this.getQueryParams();//查询条件
        getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
          if (res.success) {
            this.$refs.CardInformationSendSms.add(this.selectedRowKeys,res.result);
            this.$refs.CardInformationSendSms.title = "发送短信";
            this.$refs.CardInformationSendSms.disableSubmit = false;
          }
        })
      },

      //按导入号码发送短信弹出页面
      handleCardImportSendSms: function () {
        this.$refs.CardInformationImportSendSms.add(this.selectedRowKeys.length);
        this.$refs.CardInformationImportSendSms.title = "按导入号码发送短信";
        this.$refs.CardInformationImportSendSms.disableSubmit = false;
      },

      //按条件设置同步状态弹出页面
      handleCardImportSynchronousState: function () {

        var params = this.getQueryParams();//查询条件
        getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
          if (res.success) {

            this.$refs.CardInformationSynchronousState.add(this.selectedRowKeys,res.result);
            this.$refs.CardInformationSynchronousState.title = "按条件设置同步状态";
            this.$refs.CardInformationSynchronousState.disableSubmit = false;
          }
        })
      },

      //按条件代理不可复机弹出页面
      handleCardConditionalAgentRecovery: function () {
        var params = this.getQueryParams();//查询条件
        getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
          if (res.success) {
            this.$refs.CardInformationConditionalAgentRecovery.add(this.selectedRowKeys,res.result);
            this.$refs.CardInformationConditionalAgentRecovery.title = "按条件代理不可复机";
            this.$refs.CardInformationConditionalAgentRecovery.disableSubmit = false;
        }
      })
      },

      //按条件设置流量使用比例弹出页面
      handleCardFlowUsageRatio: function () {
        if(this.selectedRowKeys.length>0){
          this.$refs.CardInformationFlowUsageRatio.add(this.selectedRowKeys,null);
          this.$refs.CardInformationFlowUsageRatio.title = "流量使用比例";
          this.$refs.CardInformationFlowUsageRatio.disableSubmit = false;
        }else{
          var params = this.getQueryParams();//查询条件
          getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
            if (res.success) {
              this.$refs.CardInformationFlowUsageRatio.add(this.selectedRowKeys,res.result);
              this.$refs.CardInformationFlowUsageRatio.title = "流量使用比例";
              this.$refs.CardInformationFlowUsageRatio.disableSubmit = false;
            }
          })
        }
      },

      //设置自动停复机
      handleCardAutomaticShutdown: function () {
        var params = this.getQueryParams();//查询条件
        getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
          if (res.success) {
            this.$refs.CardInformationAutomaticShutdown.add(this.selectedRowKeys,res.result);
            this.$refs.CardInformationAutomaticShutdown.title = "设置自动停复机";
            this.$refs.CardInformationAutomaticShutdown.disableSubmit = false;
        }
      })
      },

      //是否实名认证
      handleCardRealNameAuthentication: function () {
        var params = this.getQueryParams();//查询条件
        getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
          if (res.success) {
            this.$refs.CardInformationRealNameAuthentication.add(this.selectedRowKeys,res.result);
            this.$refs.CardInformationRealNameAuthentication.title = "是否实名认证";
            this.$refs.CardInformationRealNameAuthentication.disableSubmit = false;
        }
      })
      },

      //设置优先级
      handleCardPriority: function () {
        var params = this.getQueryParams();//查询条件
        getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
          if (res.success) {
            this.$refs.CardInformationPriority.add(this.selectedRowKeys,res.result);
            this.$refs.CardInformationPriority.title = "设置优先级";
            this.$refs.CardInformationPriority.disableSubmit = false;
        }
      })
      },

      //卡备注
      handleCardNote: function () {
        if(this.selectedRowKeys.length>0){
          this.$refs.CardInformationNote.add(this.selectedRowKeys,null);
          this.$refs.CardInformationNote.title = "卡备注";
          this.$refs.CardInformationNote.disableSubmit = false;
        }else{
          var params = this.getQueryParams();//查询条件
          getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
            if (res.success) {
              this.$refs.CardInformationNote.add(this.selectedRowKeys,res.result);
              this.$refs.CardInformationNote.title = "卡备注";
              this.$refs.CardInformationNote.disableSubmit = false;
            }
          })
        }
      },

      //卡状态变更弹出页面
      handleCardStateChange: function () {
        //有选中的就不在查询后台
        if(this.selectedRowKeys.length>0){
          this.$refs.CardInformationStateChange.add(this.selectedRowKeys,null);
          this.$refs.CardInformationStateChange.title = "卡状态变更";
          this.$refs.CardInformationStateChange.disableSubmit = false;
        }else{
          var params = this.getQueryParams();//查询条件
          getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
            if (res.success) {
              this.$refs.CardInformationStateChange.add(this.selectedRowKeys,res.result);
              this.$refs.CardInformationStateChange.title = "卡状态变更";
              this.$refs.CardInformationStateChange.disableSubmit = false;
            }
          })
        }
      },
      //设备限速
      speedLimitChange: function () {
        //有选中的就不在查询后台
        if(this.selectedRowKeys.length>0){
          this.$refs.CardInformationSpeedLimit.add(this.selectedRowKeys,null);
          this.$refs.CardInformationSpeedLimit.title = "设备限速";
          this.$refs.CardInformationSpeedLimit.disableSubmit = false;
        }else{
          var params = this.getQueryParams();//查询条件
          getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
            if (res.success) {
              this.$refs.CardInformationSpeedLimit.add(this.selectedRowKeys,res.result);
              this.$refs.CardInformationSpeedLimit.title = "设备限速";
              this.$refs.CardInformationSpeedLimit.disableSubmit = false;
            }
          })
        }
      },
      //卡分配弹出页面
      handleCardDistribution: function () {
        if(this.selectedRowKeys.length>0){
          this.$refs.CardInformationDistribution.add(this.selectedRowKeys,null);
          this.$refs.CardInformationDistribution.title = "卡分配";
          this.$refs.CardInformationDistribution.disableSubmit = false;
          return;
        }
        var params = this.getQueryParams();//查询条件
        /*if(this.selectedRowKeys==''){
          this.$message.warning('请选择要分配的卡')
          return;
        }

let map = this.selectionRows.map(item=>{ return item.iccid});
        console.log('this.selectionRows>>>'+JSON.stringify(map));
        this.$refs.CardInformationDistribution.add(this.selectedRowKeys);
        this.$refs.CardInformationDistribution.title = "卡分配";
        this.$refs.CardInformationDistribution.disableSubmit = false;*/

        getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
          if (res.success) {

            this.$refs.CardInformationDistribution.add(res.result);
            this.$refs.CardInformationDistribution.title = "卡分配";
            this.$refs.CardInformationDistribution.disableSubmit = false;
          }
        })
      },

      //批量分配套餐
      handleCardDistributionPackage: function () {
        if(this.selectedRowKeys.length>0){
          this.$refs.CardInformationDistributionPackage.add(this.selectedRowKeys,null);
          this.$refs.CardInformationDistributionPackage.title = "分配套餐";
          this.$refs.CardInformationDistributionPackage.disableSubmit = false;
        }else{
          //查询条件
          var params = this.getQueryParams();
          if(typeof(params.customerId) == "undefined"){
            this.$message.warning('请选择客户！');
            return;
          }
          getAction('/telecomcardinformation/telecomCardInformation/listId', params).then((res) => {
            if (res.success) {
              this.$refs.CardInformationDistributionPackage.add(this.selectedRowKeys,res.result,params.operatorId);
              this.$refs.CardInformationDistributionPackage.title = "分配套餐";
              this.$refs.CardInformationDistributionPackage.disableSubmit = false;
            }
          })
        }
      },

      //批量分配套餐
      handleCardDistributionPackageOne: function (record) {

        getTelecomRefCardCostByCardId({iccid:record.iccid}).then((res)=>{
          if (res.success) {
            this.$refs.CardInformationDistributionPackageOne.add(record,res.result);
            this.$refs.CardInformationDistributionPackageOne.title = "分配套餐";
            this.$refs.CardInformationDistributionPackageOne.disableSubmit = false;
          }
        })
      },

      //卡回收
      handleCardInformationRecycling: function () {
        if (this.selectedRowKeys.length <= 0) {
          this.$message.warning('请选择一条记录！');
          return false;
        }
        let map = this.selectionRows.map(item=>{return item.iccid});
        let iccids = map.join(",");
        this.$refs.CardInformationRecycling.add(iccids);
        this.$refs.CardInformationRecycling.title = "卡回收";
        this.$refs.CardInformationRecycling.disableSubmit = false;
        /*postAction('/unicomcardinformation/unicomCardInformation/cardRecycling', {iccids: iccids}).then((res) => {
          if (res.success) {
            if (res.success) {
              this.$message.success(res.message);
              this.onClearSelected();
            } else {
              this.$message.warning(res.message);
              this.onClearSelected();
            }
          }
        })*/
      },

      //分配可充值套餐
      handleCardRechargeablePackage: function () {
        this.$refs.CardInformationRechargeablePackage.add(this.selectedRowKeys);
        this.$refs.CardInformationRechargeablePackage.title = "设置可充值套餐";
        this.$refs.CardInformationRechargeablePackage.disableSubmit = false;
      },

      //按导入卡号设置套餐
      handleCardImportCardDistributionPackage: function () {
        this.$refs.CardInformationImportCardDistributionPackage.add(this.selectedRowKeys);
        this.$refs.CardInformationImportCardDistributionPackage.title = "按导入卡号设置套餐";
        this.$refs.CardInformationImportCardDistributionPackage.disableSubmit = false;
      },

      //修改卡信息
      handleCardEditorInformation: function () {
        this.$refs.CardInformationEditorInformation.add(this.selectedRowKeys);
        this.$refs.CardInformationEditorInformation.title = "修改卡信息";
        this.$refs.CardInformationEditorInformation.disableSubmit = false;
      },

      //续费
      handleCardRenewal: function () {
        this.$refs.CardInformationRenewal.add(this.selectedRowKeys);
        this.$refs.CardInformationRenewal.title = "续费";
        this.$refs.CardInformationRenewal.disableSubmit = false;
      },
      //卡套餐转移
      handlePackageTransfer: function () {
        this.$refs.CardInformationPackageTransfer.add();
        this.$refs.CardInformationPackageTransfer.title = "卡套餐转移";
        this.$refs.CardInformationPackageTransfer.disableSubmit = false;
      },
      //数据更新
      handleDataUpdate: function () {
        this.confirmLoading = true;
        if (this.selectedRowKeys.length <= 0) {
          this.$message.warning('请选择一条记录！');
          return false;
        }else {
          let map = this.selectionRows.map(item=>{return item.iccid});
          let iccids = map.join(",");
          telecomCardDataUpdate({ids: iccids}).then((res)=>{
            if(res.success){
              this.$message.success(res.message);
              this.onClearSelected();
              this.loadData();
              this.confirmLoading = false;
            }else{
              this.$message.warning(res.message);
              this.onClearSelected();
              this.confirmLoading = false;
            }
          })

        }
      },
      //用量校正
      handleCorrection: function () {
        if (this.selectedRowKeys.length <= 0) {
          this.$message.warning('请选择一条记录！');
          return false;
        }else {

          let map = this.selectionRows.map(item=>{return item.iccid});
          let join = map.join(",");
          getAction('/telecomcardinformation/telecomCardInformation/handleCorrection', {ids: join}).then((res) => {
            if (res.success) {
              this.$message.success(res.message);
              this.onClearSelected();
            } else {
              this.$message.warning(res.message);
              this.onClearSelected();
            }
          })
        }
      },
      handleImportXls(){
        this.$refs.importModal.show()
      },

      getImportUrl(){
        return '/telecomcardinformation/telecomCardInformation/importExcel'
      },
      importOk(){
        this.loadData(1)
      },
      initData() {
        const poolId = this.$route.query.poolId;
        const status = this.$route.query.status;
        console.log('poolId>>'+poolId+'，status>>>'+status)
        this.queryParam.isFlowPool = status;
        this.queryParam.upstreamFlowPoolNumber = poolId;
        this.loadData();
      },


    },
    created() {
      this.initData()
    },




    watch: {
      '$route': 'initData'
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>