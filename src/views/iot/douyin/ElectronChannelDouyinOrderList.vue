<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6">
            <a-form-item label="订单id">
              <a-input placeholder="订单id" v-model="queryParam.orderId"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="6">
            <a-form-item  label="代理商" >
<!--              <a-input placeholder="代理商" v-model="queryParam.cusId"></a-input>-->
              <j-search-select-tag
                placeholder="请选择代理商(支持搜索)"
                v-model="queryParam.cusId"
                dict="sys_user,username,id,user_type='4'" :async="false">
              </j-search-select-tag>
            </a-form-item>
          </a-col>

          <a-col :md="4" :sm="6">
            <a-form-item label="客户姓名">
              <a-input placeholder="客户姓名" v-model="queryParam.cusName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="客户手机号">
              <a-input placeholder="客户手机号" v-model="queryParam.cusPhone"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="身份证号">
              <a-input placeholder="身份证号" v-model="queryParam.cusIdno"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="8">
            <a-form-item label="达人名称">
              <a-input placeholder="达人名称" v-model="queryParam.authorName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="8">
            <a-form-item label="发货情况">
              <a-input placeholder="达人名称" v-model="queryParam.syncExpress"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="8">
            <a-form-item label="报备情况">
              <a-input placeholder="达人名称" v-model="queryParam.syncCode"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="店铺">
              <j-dict-select-tag placeholder="请选择店铺" v-model="queryParam.shopId" dict-code="doudian_shop"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="7" :sm="9">
            <a-form-item label="销售产品">
              <j-multi-select-tag
                v-model="queryParam.agentId"
                dictCode="electron_channel_agent,agent_name,id"
                placeholder="请选择销售产品">
              </j-multi-select-tag>
            </a-form-item>
          </a-col>

          <a-col :md="4" :sm="8">
            <a-form-item label="敏感数据">
              <j-dict-select-tag
                v-model="queryParam.sensitiveIs"
                dictCode="sensitive_is"
                :async="false"
                placeholder="请选择订单状态">
              </j-dict-select-tag>
            </a-form-item>
          </a-col>

          <a-col :md="4" :sm="8">
            <a-form-item label="是否转换">
              <j-dict-select-tag
                v-model="queryParam.transferIs"
                dictCode="transfer_is"
                :async="false"
                placeholder="请选择订单状态">
              </j-dict-select-tag>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :md="8" :sm="10">
              <a-form-item label="拉单时间">
                <j-date :show-time="true" v-model="queryParam.createTime_begin" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
                <span style="width: 10px;"> ~ </span>
                <j-date :show-time="true" v-model="queryParam.createTime_end" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择结束时间"></j-date>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="10">
              <a-form-item label="下单时间">
                <j-date :show-time="true" v-model="queryParam.douyinCreateTime_begin" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
                <span style="width: 10px;"> ~ </span>
                <j-date :show-time="true" v-model="queryParam.douyinCreateTime_end" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择结束时间"></j-date>
              </a-form-item>
            </a-col>
          </template>
          <a-col :md="8" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button type="primary" icon="download" @click="handleExportXls('抖店订单')" style="margin-left: 8px">导出</a-button>
              <a-button type="primary" @click="seeReport" style="margin-left: 8px">查看报表</a-button>
              <a-button type="primary" icon="instagram" @click="manualPullOrder" style="margin-left: 8px">手动拉单</a-button>
              <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
              </a-upload>
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
<!--    <div class="table-operator">-->
<!--      <a-button type="primary" icon="download" @click="handleExportXls('electron_channel_douyin_user_relation')">导出</a-button>-->
<!--      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
<!--      </a-upload>-->
<!--    </div>-->

    <!-- table区域-begin -->
    <div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"

        @change="handleTableChange">

        <span slot="esContent1" slot-scope="text">
          <j-ellipsis :value="text" :length="5" />
        </span>

      </a-table>
    </div>

    <electronChannelDouyinUserRelation-modal ref="modalForm" @ok="modalFormOk"></electronChannelDouyinUserRelation-modal>
    <DouyinOrderReportModal ref="DouyinOrderReportModal" @ok="modalFormOk"></DouyinOrderReportModal>
    <manual-pull-douyin-order-modal ref="ManualPullDouyinOrderModal" @ok="modalFormOk"></manual-pull-douyin-order-modal>
  </a-card>
</template>

<script>
  import ManualPullDouyinOrderModal from "@views/iot/douyin/modules/ManualPullDouyinOrderModal";
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import JEllipsis from "@/components/jeecg/JEllipsis";
  import ElectronChannelDouyinUserRelationModal from './modules/ElectronChannelDouyinUserRelationModal'
  import JMultiSelectTag from '@/components/dict/JMultiSelectTag'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import JDate from '@/components/jeecg/JDate'
  import DouyinOrderReportModal from "./modules/DouyinOrderReportModal";
  import JTreeSelect from '@/components/jeecg/JTreeSelect'

  export default {
    name: "ElectronChannelDouyinOrderList",
    mixins:[JeecgListMixin],
    components: {
      ElectronChannelDouyinUserRelationModal,
      JEllipsis,
      JMultiSelectTag,
      DouyinOrderReportModal,
      JDate,
      JTreeSelect,
      JSearchSelectTag,
      ManualPullDouyinOrderModal
    },
    data () {
      return {
        isorter:{
          column: 'douyinCreateTime',
          order: 'desc',
        },
        description: 'electron_channel_douyin_order管理页面',
        // 表头
        columns: [
          // {
          //   title: '#',
          //   dataIndex: '',
          //   key:'rowIndex',
          //   width:60,
          //   align:"center",
          //   customRender:function (t,r,index) {
          //     return parseInt(index)+1;
          //   }
          // },
          {
            title:'店铺订单id',
            align:"center",
            dataIndex: 'orderId'
          },
          {
            title:'代理商',
            align:"center",
            dataIndex: 'cusId_dictText'
          },
          {
            title:'产品名称',
            align:"center",
            dataIndex: 'agentId_dictText'
          },
          {
            title:'客户姓名',
            align:"center",
            dataIndex: 'cusName',
            scopedSlots: {customRender: 'esContent1'},
          },
          {
            title:'客户手机号',
            align:"center",
            dataIndex: 'cusPhone',
            scopedSlots: {customRender: 'esContent1'},
          },
          {
            title:'身份证号',
            align:"center",
            dataIndex: 'cusIdno',
            scopedSlots: {customRender: 'esContent1'},
          },
          {
            title:'省',
            align:"center",
            dataIndex: 'province'
          },
          {
            title:'市',
            align:"center",
            dataIndex: 'city'
          },
          {
            title:'区',
            align:"center",
            dataIndex: 'district'
          },
          {
            title:'街道',
            align:"center",
            dataIndex: 'street'
          },
          {
            title:'详细地址',
            align:"center",
            dataIndex: 'detailAddr',
            scopedSlots: {customRender: 'esContent1'},
          },
          {
            title:'是否转换',
            align:"center",
            dataIndex: 'transferIs_dictText',
          },
          // {
          //   title:'支付金额(分)',
          //   align:"center",
          //   dataIndex: 'payAmount',
          // },
          {
            title:'报备',
            align:"center",
            dataIndex: 'syncCode',
          },
          {
            title:'发货',
            align:"center",
            dataIndex: 'syncExpress',
          },
          {
            title:'达人名称',
            align:"center",
            dataIndex: 'authorName',
          },
          {
            title:'店铺',
            align:"center",
            dataIndex: 'shopId_dictText',
          },
          {
            title:'下单时间',
            align:"center",
            dataIndex: 'douyinCreateTime',
          },
          {
            title:'拉单时间',
            align:"center",
            dataIndex: 'createTime',
          },


        ],
        url: {
          list: "/douyin/electronChannelDouyinOrder/list",
          delete: "/douyin/electronChannelDouyinOrder/delete",
          deleteBatch: "/douyin/electronChannelDouyinOrder/deleteBatch",
          exportXlsUrl: "/douyin/electronChannelDouyinOrder/exportXls",
          importExcelUrl: "douyin/electronChannelDouyinOrder/importExcel",
        },
        dictOptions:{
        },
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      initDictConfig(){
      },
      seeReport() {
        this.$refs.DouyinOrderReportModal.see();
        this.$refs.DouyinOrderReportModal.title = "报表";
        this.$refs.DouyinOrderReportModal.disableSubmit = false;
      },
      manualPullOrder() {
        this.$refs.ManualPullDouyinOrderModal.add();
        this.$refs.ManualPullDouyinOrderModal.title = "手动拉单";
        this.$refs.ManualPullDouyinOrderModal.disableSubmit = false;
      }

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>