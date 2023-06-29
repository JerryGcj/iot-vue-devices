<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <a-col :md="4" :sm="6">
            <a-form-item label="创建人">
              <a-input placeholder="请输入创建人" v-model="queryParam.createBy"></a-input>
            </a-form-item>
          </a-col>
          <a-col v-has="'electonChannelOrderWaistList:selectCustomerIdDls'" :md="4" :sm="6">
            <a-form-item label="用户账号">
              <j-search-select-tag
                placeholder="请选择用户账号"
                v-model="queryParam.cusId"
                dict="sys_user,username,id">
              </j-search-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="单号">
              <a-input placeholder="请输入单号" v-model="queryParam.id"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="客户姓名">
              <a-input placeholder="请输入客户姓名" v-model="queryParam.cusName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="客户手机号">
              <a-input placeholder="请输入客户手机号" v-model="queryParam.cusPhone"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="运单号">
              <a-input placeholder="请输入运单号" v-model="queryParam.expressNo"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="4" :sm="6">
            <a-form-item label="接入号">
              <a-input placeholder="请输入接入号" v-model="queryParam.accessNumber"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="8">
            <a-form-item label="订单状态">
              <a-select
                allowClear="true"
                v-model="orderStatusValue"
                mode="multiple"
                style="width: 100%"
                placeholder="请选择"
                :options="orderStatusDictOptions"
                :filterOption="likeQuery"
                :autoClearSearchValue="false"
                :maxTagTextLength=3
                :maxTagCount=3
              ></a-select>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="6">
            <a-form-item label="外部订单号">
              <a-input placeholder="请输入外部订单号" v-model="queryParam.outTradeNo"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="渠道名称">
              <a-select
                allowClear="true"
                v-model="channelNameValue"
                mode="multiple"
                style="width: 100%"
                placeholder="请选择"
                :options="channelNameDictOptions"
                :filterOption="likeQuery"
                :autoClearSearchValue="false"
                :maxTagTextLength=3
                :maxTagCount=3
              ></a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="销售产品">
              <j-multi-select-tag
                v-model="queryParam.agentId"
                dictCode="electron_channel_agent,agent_name,id,agent_simple_name like 'haeb-telecom'"
                placeholder="请选择销售产品">
              </j-multi-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="8">
            <a-form-item label="作废原因">
              <a-input placeholder="请输入作废原因" v-model="queryParam.cancelMsg"></a-input>
            </a-form-item>
          </a-col>
            
          <a-col :md="4" :sm="8">
            <a-form-item label="订单来源">
              <j-multi-select-tag
                v-model="queryParam.orderSource"
                dictCode="order_source"
                :async="false"
                placeholder="请选择销售产品">
              </j-multi-select-tag>
            </a-form-item>
          </a-col>

       
          <template v-if="toggleSearchStatus">
            <a-col :md="8" :sm="10">
              <a-form-item label="收单日期">
                <j-date :show-time="true" v-model="queryParam.createTime_begin" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
                <span style="width: 10px;"> ~ </span>
                <j-date :show-time="true" v-model="queryParam.createTime_end" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择结束时间"></j-date>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="10">
              <a-form-item label="提单日期">
                <j-date :show-time="true" v-model="queryParam.commitTime_begin" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
                <span style="width: 10px;"> ~ </span>
                <j-date :show-time="true" v-model="queryParam.commitTime_end" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择结束时间"></j-date>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="10">
              <a-form-item label="激活日期">
                <j-date :show-time="true" v-model="queryParam.activationDate_begin" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
                <span style="width: 10px;"> ~ </span>
                <j-date :show-time="true" v-model="queryParam.activationDate_end" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择结束时间"></j-date>
              </a-form-item>
            </a-col>
            <a-col :md="4" :sm="4">
              <a-form-item label="上游单号">
                <a-input placeholder="请输入上游单号" v-model="queryParam.orderNum"></a-input>
              </a-form-item>
            </a-col>
          </template>
          <a-col :md="4" :sm="6" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button v-has="'electronChannelWaistOrderList:export'" type="primary" icon="download" @click="handleExportXls('腰部订单')" style="margin-left: 8px" :disabled="isDisable">导出</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">

      <a-button type="primary" icon="form" v-has="'electronChannelWaistOrderList:seeReport'" @click="seeReport" style="margin-left: 8px">查看报表</a-button>
      <a-button type="primary" icon="form" v-has="'electronChannelWaistOrderList:seeReport2'" @click="seeReport2" style="margin-left: 8px">查看激活报表</a-button>
      <a-button v-has="'electronChannelWaistOrderList:import'" @click="handleImportXls" type="primary" icon="plus">导入订单</a-button>
      <a-button v-has="'electronChannelWaistOrderList:manualSubmit'" @click="manualSubmit" type="primary" icon="plus">手动提单</a-button>
    
    </div>

    <!-- table区域-begin -->
    <div>
      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :scroll="{ x: 2400}"
        :pagination="ipagination"
        :loading="loading"

        @change="handleTableChange">

        <span slot="idno" slot-scope="id">
          <a v-text="id" @click="refreshStatus(id)"></a>
        </span>

         <span slot="cardContent" slot-scope="text">
          <j-ellipsis :value="text" :length="5" />
        </span>
        <span slot="saleProduct" slot-scope="text">
          <j-ellipsis :value="text" :length="5" />
        </span>
        <span slot="esContent1" slot-scope="text">
          <j-ellipsis :value="text" :length="5" />
        </span>

        <span slot="esContent2" slot-scope="text">
          <j-ellipsis :value="text" :length="8" />
        </span>



        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无此图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="图片不存在" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无此文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="uploadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
          <a v-has="'electronChannelWaistOrderList:edit'" @click="handleEdit(record)">编辑<a-divider type="vertical" /></a>
           <a v-if="record.orderStatus=='3'" @click="commitErrorReason(record.id)">查看原因<a-divider type="vertical" /></a>
          <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
            <a v-has="'electronChannelWaistOrderList:delete'">删除</a>
          </a-popconfirm>
        </span>

      </a-table>
    </div>
    <electron-query-commit-error-modal ref="errorModal"></electron-query-commit-error-modal>
    <electronChannelOrder-modal ref="modalForm" @ok="modalFormOk"></electronChannelOrder-modal>
    <report-modal ref="reportModal" @ok="modalFormOk"></report-modal>
    <report2-modal ref="report2Modal" @ok="modalFormOk"></report2-modal>
    <api-return-modal ref="apiReturnModal" @ok="modalFormOk"></api-return-modal>
    <j-import-modal ref="importModal" :url="getImportUrl()" @ok="importOk"></j-import-modal>
    <manual-submit-order-modal ref="manualSubmitModal" @ok="modalFormOk"></manual-submit-order-modal>
  </a-card>
</template>

<script>
  import JEllipsis from "@/components/jeecg/JEllipsis";
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronQueryCommitErrorModal from './modules/ElectronQueryCommitErrorModal'
  import ElectronChannelOrderModal from './modules/ElectronChannelOrderModal'
  import { postAction,getAction,downFile} from '@/api/manage'
  import ReportModal from "./modules/ReportModal";
  import Report2Modal from "./modules/Report2Modal";
  import JImportModal from './modules/ElectronOrderImportModal'
  import JDate from '@/components/jeecg/JDate'
  import ApiReturnModal from "./modules/ApiReturnModal";
  import {queryLowerAgent} from '@/api/api'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import JMultiSelectTag from '@/components/dict/JMultiSelectTag'
  import ManualSubmitOrderModal from "./modules/ManualSubmitOrderModal";

  export default {
    name: "ElectronOrderSdTelecom00List",
    mixins:[JeecgListMixin],
    components: {
      ReportModal,
      Report2Modal,
      ApiReturnModal,
      ElectronChannelOrderModal,
      ElectronQueryCommitErrorModal,
      JTreeSelect,
      JEllipsis,
      JDate,
      JImportModal,
      JSearchSelectTag,
      JMultiSelectTag,
      ManualSubmitOrderModal
    },
    data () {
      return {
        orderStatusValue:[],
        channelNameValue:[],
        orderStatusDictOptions:[],
        channelNameDictOptions:[],

        operatorValue: [],
        isDisable: false,
        description: '腰部订单',
        userData:[],
        // 表头
          columns: [
              {
                  title:'单号',
                  align:"center",
                  dataIndex: 'id',
                  scopedSlots: {customRender: 'idno'},
                  width: 200
              },

            {
              title:'上游单号',
              align:"center",
              dataIndex: 'orderNum',
              width: 250
            },
              {
                  title:'创建人',
                  align:"center",
                  dataIndex: 'createBy',
                  scopedSlots: {customRender: 'esContent1'},
                  width: 100
              },
              {
                  title:'用户账号',
                  align:"center",
                  dataIndex: 'userName',
                  scopedSlots: {customRender: 'esContent2'},
                  width: 100
              },
              {
                  title:'代理商',
                  align:"center",
                  dataIndex: 'cusId_dictText',
                  width: 100
              },
              {
                  title:'客户姓名',
                  align:"center",
                  dataIndex: 'cusName',
                  width:60
              },
              {
                  title:'客户手机号',
                  align:"center",
                  dataIndex: 'cusPhone',
                  width:100,
              },
              {
                  title:'省',
                  align:"center",
                  dataIndex: 'province',
                  width:60
              },
              {
                  title:'市',
                  align:"center",
                  dataIndex: 'city',
                  width:60
              },
              {
                  title:'区',
                  align:"center",
                  dataIndex: 'district',
                  width:60
              },
              {
                  title:'详细地址',
                  align:"left",
                  dataIndex: 'detailAddr',
                  scopedSlots: {customRender: 'esContent1'},
                  width: 100
              },
            {
              title:'身份证号',
              align:"center",
              dataIndex: 'cusIdno',
              scopedSlots: {customRender: 'cardContent'},
              width: 100
            },
            {
              title:'销售产品',
              align:"center",
              dataIndex: 'agentId_dictText',
              scopedSlots: {customRender: 'saleProduct'},
              width: 100
            },
            {
              title:'渠道名称',
              align:"center",
              dataIndex: 'channelName',
              width: 100
            },
            {
              title:'外部订单号',
              align:"center",
              dataIndex: 'outTradeNo',
              width: 200
            },
            {
              title:'订单来源',
              align:"center",
              dataIndex: 'orderSource_dictText',
              width: 100
            },
            {
              title:'订单状态',
              align:"center",
              dataIndex: 'orderStatus_dictText',
              width: 100
            },
            {
              title:'作废原因',
              align:"center",
              dataIndex: 'cancelMsg',
              scopedSlots: {customRender: 'esContent2'},
              width: 100
            },
            {
              title:'收单日期',
              align:"center",
              dataIndex: 'createTime',
              width:100
            },
            {
              title:'提单日期',
              align:"center",
              dataIndex: 'commitTime',
              width:100
            },
            {
              title:'激活日期',
              align:"center",
              dataIndex: 'activationDate',
              width:100
            },
            {
              title:'快递单号',
              align:"center",
              dataIndex: 'expressNo',
              width: 130
            },
            {
              title:'快递公司',
              align:"center",
              dataIndex: 'expressCompany',
              width: 100
            },
            {
              title:'接入号',
              align:"center",
              dataIndex: 'accessNumber',
              width: 100
            },
              {
                  title: '操作',
                  dataIndex: 'action',
                  align:"center",
                  width:90,
                  scopedSlots: { customRender: 'action' },
              }
          ],
        url: {
          list: "/electronregular/electronChannelOrderRegular/list?simpleName="+'haeb-telecom',
          delete: "/electronregular/electronChannelOrderRegular/delete",
          deleteBatch: "/electronregular/electronChannelOrderRegular/deleteBatch",
          exportXlsUrl: "/electronregular/electronChannelOrderRegular/exportXls?simpleName=" + 'haeb-telecom',
          importExcelUrl: "electronregular/electronChannelOrderRegular/importExcel",
          queryImportBatch: "electronchannelorder/electronChannelOrder/queryImportBatch",
          syncExpressNoAndIccid: "electronchannelorder/electronChannelOrder/syncExpressNoAndIccid",
          importOnlineOrder: "electronchannelorder/electronChannelOrder/importOnlineOrder",
          queryOrderStatusDict:"/electronregular/electronChannelOrderRegular/queryOrderStatusDict",
          queryChannelNameDict:"/electronregular/electronChannelOrderRegular/queryChannelNameDict"
        },
        dictOptions:{
        },
        importBatch:[],
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
      syncExpressNoAndIccid: function(){
        return `${window._CONFIG['domianURL']}/${this.url.syncExpressNoAndIccid}`;
      },
      accessNumberUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.accessNumberUrl}`;
      },
      importOnlineOrder: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importOnlineOrder}`;
      }
    },
    created() {
      this.queryOrderStatusDict();
      this.queryChannelNameDict();

      this.operatorValue =[];
    },
    methods: {
      searchQuery(){
        this.queryParam.orderStatus = this.orderStatusValue.join(",")
        this.queryParam.channelName = this.channelNameValue.join(",")
        this.loadData(1)
      },
      queryOrderStatusDict() {
        getAction(this.url.queryOrderStatusDict).then((res)=>{
          if(res.success){
            this.orderStatusDictOptions = res.result;
          }
        })
      },
      queryChannelNameDict() {
        getAction(this.url.queryChannelNameDict).then((res)=>{
          if(res.success){
            this.channelNameDictOptions = res.result;
          }
        })
      },
      likeQuery(input, option) {
        return ( option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0 );
      },

      handleExportXls(fileName) {
        let param = {...this.queryParam};
        if (JSON.stringify(param) !== '{}'){
        this.isDisable = true;
        if(!fileName || typeof fileName != "string"){
          fileName = "导出文件"
        }
        if (this.selectedRowKeys && this.selectedRowKeys.length > 0) {
          param['selections'] = this.selectedRowKeys.join(",")
        }
        console.log("导出参数",param)
        downFile(this.url.exportXlsUrl,param).then((data)=>{
          setTimeout(() => {
            this.isDisable = false;
            if (!data) {
              this.$message.warning("文件下载失败")
              return
            }
            if (typeof window.navigator.msSaveBlob !== 'undefined') {
              window.navigator.msSaveBlob(new Blob([data]), fileName+'.xls')
            }else{
              let url = window.URL.createObjectURL(new Blob([data]))
              let link = document.createElement('a')
              link.style.display = 'none'
              link.href = url
              link.setAttribute('download', fileName+'.xls')
              document.body.appendChild(link)
              link.click()
              document.body.removeChild(link); //下载完成移除元素
              window.URL.revokeObjectURL(url); //释放掉blob对象
            }
          },1000)

        })
        } else {
          this.$message.error("请选择导出条件！")
        }
      },

      autoDelivery:function(){
        let batch = this.queryParam.importBatch;
        if(!batch){
          this.$message.warn("请选择要发货的批次！")
          return;
        }
        let params = {"importBatch":batch};
        postAction('electronchannelorder/electronChannelOrder/pushUpstreamOrder/',params).then((res) => {
          if (res.success) {
            this.$message.success(res.message)
          }else{
            this.$message.warn(res.message)
          }
        })
      },
      commitErrorReason:function(id){
        getAction('electronupstream/electronChannelUpstreamRecord/queryByOrderId?orderId='+id,null).then((res) => {
          if (res.success) {
            this.$refs.errorModal.title = "失败原因";
            this.$refs.errorModal.errorMsg = res.result.upstreamMsg;
            this.$refs.errorModal.add();
          }
        })

      },
      refreshStatus:function(id){
        getAction('electronregular/electronChannelOrderRegular/queryByOrderId?orderId='+id,null).then((res) => {
          console.log(res)
          if (res.success) {
            this.$refs.apiReturnModal.add(res.result);
            this.$refs.apiReturnModal.disableSubmit = true;
          }else{
            this.$message.warning(res.message)
          }
        })

      },
      //查看激活
      graphReport: function () {
        this.$refs.graphReport.add();
        this.$refs.graphReport.title = "查看激活";
        this.$refs.graphReport.disableSubmit = false;
      },
      initDictConfig(){
      },
      //查看报表
      seeReport: function () {
        this.$refs.reportModal.see();
        this.$refs.reportModal.title = "报表";
        this.$refs.reportModal.disableSubmit = false;
      },
      //查看报表
      seeReport2: function () {
        this.$refs.report2Modal.see("haeb-telecom");
        this.$refs.report2Modal.title = "报表";
        this.$refs.report2Modal.disableSubmit = false;
      },
      //手动提单
      manualSubmit: function () {
        this.$refs.manualSubmitModal.add();
        this.$refs.manualSubmitModal.title = "手动提单";
        this.$refs.manualSubmitModal.disableSubmit = false;
      },
      getImportUrl(){
        return '/electronregular/electronChannelOrderRegular/importExcel/'
      },
      searchReset() {
        this.orderStatusValue = []
        this.channelNameValue = []
        this.queryParam = {}
        this.loadData(1);
      },
    }
  }
</script>
<style scoped lang="less">
  @import '~@assets/less/common.less';
  /deep/ .ant-calendar-picker {
  min-width: 33%;
  max-width: 40%;
}
</style>