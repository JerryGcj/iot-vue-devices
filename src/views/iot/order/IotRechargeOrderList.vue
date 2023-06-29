<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="手机号">
              <a-input placeholder="请输入用户手机号" v-model="queryParam.mobile"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="充值状态">
              <j-dict-select-tag v-model="queryParam.status" placeholder="请选择充值状态" dictCode="pay_state"/>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="运营商">
              <j-dict-select-tag v-model="queryParam.operatorType" placeholder="请选择运营商" dictCode="operator_type"/>
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="12">
            <a-form-item label="充值时间">
              <j-date v-model="queryParam.createTime_begin" date-format="YYYY-MM-DD 00:00:00" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
              <span style="width: 10px;">~</span>
              <j-date v-model="queryParam.createTime_end" date-format="YYYY-MM-DD 23:59:59" class="query-group-cust" placeholder="请选择结束时间"></j-date>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button type="primary" icon="download" @click="handleExportXls('用户充值记录')" style="margin-left: 8px">导出</a-button>
              <a-button type="primary" @click="seeReport" style="margin-left: 8px">查看报表</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->

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
        :rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"

        @change="handleTableChange">

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
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>
    <recharge-order-report-modal ref="RechargeOrderReportModal" @ok="modalFormOk"></recharge-order-report-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import JDate from '@/components/jeecg/JDate'
  import RechargeOrderReportModal from "./modules/RechargeOrderReportModal";

  export default {
    name: "IotRechargeOrderList",
    mixins:[JeecgListMixin],
    components: {
      RechargeOrderReportModal,
      JDate
    },
    data () {
      return {
        description: 'iot_recharge_order管理页面',
        // 表头
        columns: [
          {
            title:'运营商',
            align:"center",
            dataIndex: 'operatorType_dictText'
          },
          {
            title:'充值用户手机号',
            align:"center",
            dataIndex: 'mobile'
          },
          /*{
            title:'推广员',
            align:"center",
            dataIndex: 'promoteId_dictText'
          },*/
          {
            title:'微信支付单号',
            align:"center",
            dataIndex: 'transactionId'
          },
          {
            title:'充值产品',
            align:"center",
            dataIndex: 'productId_dictText'
          },
          {
            title:'充值金额(元)',
            align:"center",
            dataIndex: 'money'
          },
          {
            title:'到账金额(元)',
            align:"center",
            dataIndex: 'arrivalAccount'
          },
          {
            title:'状态',
            align:"center",
            dataIndex: 'status_dictText'
          },
          {
            title:'企业名称',
            align:"center",
            dataIndex: 'storeId_dictText'
          },
          {
            title:'充值时间',
            align:"center",
            dataIndex: 'createTime'
          }
        ],
        url: {
          list: "/order/iotRechargeOrder/list",
          delete: "/order/iotRechargeOrder/delete",
          deleteBatch: "/order/iotRechargeOrder/deleteBatch",
          exportXlsUrl: "/order/iotRechargeOrder/exportXls",
          importExcelUrl: "order/iotRechargeOrder/importExcel",
        },
        dictOptions:{
        },
        queryParam: {
          createTime_begin: this.dateFormat(new Date()),
          createTime_end: this.dateFormat(new Date())
        }
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
        this.$refs.RechargeOrderReportModal.see();
        this.$refs.RechargeOrderReportModal.title = "报表";
        this.$refs.RechargeOrderReportModal.disableSubmit = false;
      }

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>