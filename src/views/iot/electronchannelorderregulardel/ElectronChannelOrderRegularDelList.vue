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
          <!--          <a-col v-has="'electonChannelOrderWaistList:selectCustomerId'" :md="4" :sm="6">-->
          <!--            <a-form-item label="客户" >-->
          <!--              <j-tree-select-->
          <!--                v-model="queryParam.cusId"-->
          <!--                placeholder="请选择客户"-->
          <!--                dict="sys_user,user_company,id"-->
          <!--                condition='{"del_flag":"0"}'-->
          <!--                pidField="create_by_id"-->
          <!--                pidValue=""/>-->
          <!--            </a-form-item>-->
          <!--          </a-col>-->
          <!--          <a-col v-has="'electonChannelOrderWaistList:selectCustomerIdDls'" :md="4" :sm="6">-->
          <!--            <a-form-item label="代理商">-->
          <!--              &lt;!&ndash;<a-select v-model="queryParam.cusId"-->
          <!--                        placeholder="请选择客户">-->
          <!--                <a-select-option v-for="d in userData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>-->
          <!--              </a-select>&ndash;&gt;-->
          <!--              <j-search-select-tag-->
          <!--                placeholder="请选择代理商"-->
          <!--                v-model="queryParam.cusId"-->
          <!--                dict="sys_user,realname,id"-->
          <!--                :async="true">-->
          <!--              </j-search-select-tag>-->
          <!--            </a-form-item>-->
          <!--          </a-col>-->
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
          <!--<a-col :md="6" :sm="8">-->
          <!--<a-form-item label="iccid">-->
          <!--<a-input placeholder="请输入iccid" v-model="queryParam.iccid"></a-input>-->
          <!--</a-form-item>-->
          <!--</a-col>-->
          <a-col :md="4" :sm="6">
            <a-form-item label="接入号">
              <a-input placeholder="请输入接入号" v-model="queryParam.accessNumber"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="8">
            <a-form-item label="订单状态">
              <a-select v-model="queryParam.orderStatus" placeholder="请选择订单状态" allowClear>
                <a-select-option value="1">初始化</a-select-option>
                <a-select-option value="2">提交成功</a-select-option>
                <a-select-option value="3">提交失败</a-select-option>
                <a-select-option value="4">已发货未签收</a-select-option>
                <a-select-option value="7">已签收未激活</a-select-option>
                <a-select-option value="6">已激活</a-select-option>
                <a-select-option value="5">订单作废</a-select-option>
                <a-select-option value="11">挂起</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="6">
            <a-form-item label="外部订单号">
              <a-input placeholder="请输入外部订单号" v-model="queryParam.outTradeNo"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="渠道名称">
              <a-input placeholder="请输入渠道名称" v-model="queryParam.channelName"></a-input>
            </a-form-item>
          </a-col>
<!--          <a-col :md="6" :sm="8">-->
<!--            <a-form-item label="销售产品">-->
<!--              <j-multi-select-tag-->
<!--                v-model="queryParam.agentId"-->
<!--                dictCode="electron_channel_agent,agent_name,id"-->
<!--                placeholder="请选择销售产品">-->
<!--              </j-multi-select-tag>-->
<!--            </a-form-item>-->
<!--          </a-col>-->
          <a-col :md="5" :sm="8">
            <a-form-item label="作废原因">
              <a-input placeholder="请输入作废原因" v-model="queryParam.cancelMsg"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="8">
            <a-form-item label="订单来源">
              <a-select v-model="queryParam.orderSource" placeholder="请选择订单来源" allowClear>
                <a-select-option value="1">落地页</a-select-option>
                <a-select-option value="2">订单导入</a-select-option>
                <a-select-option value="3">抖店抓取</a-select-option>
                <a-select-option value="4">快手抓取</a-select-option>
              </a-select>
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
              <a-button v-has="'ElectronChannelOrderRegularDelList:cancelOrderModal'" type="primary" @click="cancelOrderModal" style="margin-left: 8px" >恢复订单</a-button>
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
<!--      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>-->
<!--      <a-button type="primary" icon="download" @click="handleExportXls('electron_channel_order_regular_del')">导出</a-button>-->
<!--      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
<!--        <a-button type="primary" icon="import">导入</a-button>-->
<!--      </a-upload>-->
<!--      <a-dropdown v-if="selectedRowKeys.length > 0">-->
<!--        <a-menu slot="overlay">-->
<!--          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>-->
<!--        </a-menu>-->
<!--        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>-->
<!--      </a-dropdown>-->
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

          <a-dropdown>
            <a class="ant-dropdown-link">更多<a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定恢复订单吗?" @confirm="() => cancelOrder(record.id)">
                  <a>恢复订单</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <electronChannelOrderRegularDel-modal ref="modalForm" @ok="modalFormOk"></electronChannelOrderRegularDel-modal>
    <cancel-order-modal ref="cancelOrderModal" @ok="modalFormOk"></cancel-order-modal>
  </a-card>
</template>

<script>

  import JEllipsis from "@/components/jeecg/JEllipsis";
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronChannelOrderRegularDelModal from './modules/ElectronChannelOrderRegularDelModal'
  import { httpAction } from '@/api/manage'
  import cancelOrderModal from './modules/cancelOrderModal'
  import JDate from '@/components/jeecg/JDate'


  export default {
    name: "ElectronChannelOrderRegularDelList",
    mixins:[JeecgListMixin],
    components: {
      ElectronChannelOrderRegularDelModal,
      JEllipsis,
      cancelOrderModal,
      JDate

    },
    data () {
      return {
        description: 'electron_channel_order_regular_del管理页面',
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
          list: "/electronchannelorderregulardel/electronChannelOrderRegularDel/list",
          delete: "/electronchannelorderregulardel/electronChannelOrderRegularDel/delete",
          deleteBatch: "/electronchannelorderregulardel/electronChannelOrderRegularDel/deleteBatch",
          exportXlsUrl: "/electronchannelorderregulardel/electronChannelOrderRegularDel/exportXls",
          importExcelUrl: "electronchannelorderregulardel/electronChannelOrderRegularDel/importExcel",
          cancelOrder:"/electronchannelorderregulardel/electronChannelOrderRegularDel/cancelOrder"
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
      searchQuery() {
        this.loadData(1);
      },
      cancelOrder(id) {
        const jsonData = {"id":id};
        httpAction(this.url.cancelOrder,jsonData,'post').then((res)=> {
          if(res.success){
            this.$message.success(res.message);
            this.searchQuery();
          }else {
            this.$message.warning(res.message);
          };
          }).finally(() => {
          that.confirmLoading = false;
          that.close();
        })
        },
      cancelOrderModal() {
        this.$refs.cancelOrderModal.add();
        this.$refs.cancelOrderModal.title = "恢复订单";
        this.$refs.cancelOrderModal.disableSubmit = false;

      }

    },

  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>