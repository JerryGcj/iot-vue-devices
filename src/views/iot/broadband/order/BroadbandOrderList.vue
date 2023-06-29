<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6">
            <a-form-item label="单号">
              <a-input placeholder="请输入单号" v-model="queryParam.id"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="客户姓名">
              <a-input placeholder="请输入" v-model="queryParam.cusName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="身份证号">
              <a-input placeholder="请输入" v-model="queryParam.cusIdno"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="客户手机号">
              <a-input placeholder="请输入" v-model="queryParam.cusPhone"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="上游单号">
              <a-input placeholder="请输入" v-model="queryParam.orderNum"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="宽带账户">
              <a-input placeholder="请输入" v-model="queryParam.account"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="订单状态">
             <j-dict-select-tag v-model="queryParam.orderStatus" dict-code="broadband_order_status"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="6">
            <a-form-item label="外部订单号">
              <a-input placeholder="请输入" v-model="queryParam.outTradeNo"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="渠道名称">
              <a-input placeholder="请输入" v-model="queryParam.channelName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="销售产品">
              <j-multi-select-tag
                v-model="queryParam.agentId"
                dictCode="broadband_product,product_name,id"
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
<!--            <a-col :md="4" :sm="4">-->
<!--              <a-form-item label="上游单号">-->
<!--                <a-input placeholder="请输入上游单号" v-model="queryParam.orderNum"></a-input>-->
<!--              </a-form-item>-->
<!--            </a-col>-->
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
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('broadband_order')">导出</a-button>
<!--      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
<!--        <a-button type="primary" icon="import">导入</a-button>-->
<!--      </a-upload>-->
      <a-button  @click="handleImportXls" type="primary" icon="plus">导入订单</a-button>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
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
        :scroll="{ x: 2400}"
        :rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        
        @change="handleTableChange">

        <span slot="esContent2" slot-scope="text">
          <j-ellipsis :value="text" :length="8" />
        </span>
         <span slot="idno" slot-scope="id">
          <a v-text="id" @click="refreshStatus(id)"></a>
        </span>
         <span slot="saleProduct" slot-scope="text">
          <j-ellipsis :value="text" :length="10" />
        </span>
        <span slot="esContent1" slot-scope="text">
          <j-ellipsis :value="text" :length="5" />
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
    <api-return-modal ref="apiReturnModal" @ok="modalFormOk"></api-return-modal>
    <broadbandOrder-modal ref="modalForm" @ok="modalFormOk"></broadbandOrder-modal>
    <j-import-modal ref="importModal" :url="getImportUrl()" @ok="modalFormOk"></j-import-modal>
  </a-card>
</template>

<script>
  import JEllipsis from "@/components/jeecg/JEllipsis";
  import ApiReturnModal from '@/views/iot/electronchannelorder/modules/ApiReturnModal'
  import JImportModal from './modules/BroadbandOrderImportModal'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import BroadbandOrderModal from './modules/BroadbandOrderModal'
  import JDate from '@/components/jeecg/JDate'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import JMultiSelectTag from '@/components/dict/JMultiSelectTag'
  import {getAction} from "@api/manage";
  export default {
    name: "BroadbandOrderList",
    mixins:[JeecgListMixin],
    components: {
      BroadbandOrderModal,JDate,JSearchSelectTag,JMultiSelectTag,JImportModal,ApiReturnModal,JEllipsis
    },
    data () {
      return {
        description: 'broadband_order管理页面',
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
            title:'单号',
            align:"center",
            dataIndex: 'id',
            scopedSlots: {customRender: 'idno'},
          },
          {
            title:'外单号',
            align:"center",
            dataIndex: 'outTradeNo',
          },
          {
            title:'上游单号',
            align:"center",
            dataIndex: 'orderNum',
          },
          // {
          //   title:'代理商',
          //   align:"center",
          //   dataIndex: 'cusId_dictText',
          //   width: 100
          // },
          {
            title:'姓名',
            align:"center",
            dataIndex: 'cusName'
          },
          {
            title:'身份证号',
            align:"center",
            dataIndex: 'cusIdno'
          },
          {
            title:'手机号',
            align:"center",
            dataIndex: 'cusPhone'
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
            title:'详细地址',
            align:"left",
            dataIndex: 'detailAddr',
            scopedSlots: {customRender: 'esContent1'}
          },
          {
            title:'订单状态',
            align:"center",
            dataIndex: 'orderStatus_dictText'
          },
          // {
          //   title:'身份证号',
          //   align:"center",
          //   dataIndex: 'cusIdno',
          //   scopedSlots: {customRender: 'cardContent'},
          //   width: 100
          // },
          {
            title:'宽带产品',
            align:"center",
            dataIndex: 'productId_dictText',
            scopedSlots: {customRender: 'saleProduct'}
          },
          // {
          //   title:'快递单号',
          //   align:"center",
          //   dataIndex: 'expressNo',
          //   width: 130
          // },
          // {
          //   title:'快递公司',
          //   align:"center",
          //   dataIndex: 'expressCompany',
          //   width: 100
          // },
          {
            title:'宽带账户',
            align:"center",
            dataIndex: 'account'
          },
          {
            title:'缴费金额',
            align:"center",
            dataIndex: 'amount'
          },
          {
            title:'渠道名称',
            align:"center",
            dataIndex: 'channelName'
          },
          {
            title:'收单日期',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title:'提单日期',
            align:"center",
            dataIndex: 'commitTime'
          },
          {
            title:'激活日期',
            align:"center",
            dataIndex: 'activationDate'
          },
          {
            title:'作废原因',
            align:"center",
            dataIndex: 'cancelMsg',
            scopedSlots: {customRender: 'esContent2'}
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/broadbank/broadbandOrder/list",
          delete: "/broadbank/broadbandOrder/delete",
          deleteBatch: "/broadbank/broadbandOrder/deleteBatch",
          exportXlsUrl: "/broadbank/broadbandOrder/exportXls",
          importExcelUrl: "broadbank/broadbandOrder/importExcel",
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
      refreshStatus:function(id){
        getAction('broadbank/broadbandOrder/queryByOrderId?orderId='+id,null).then((res) => {
          console.log(res)
          if (res.success) {
            this.$refs.apiReturnModal.add(res.result);
            this.$refs.apiReturnModal.disableSubmit = true;
          }else{
            this.$message.warning(res.message)
          }
        })

      },
      initDictConfig(){
      },
      /* 显示导入对话框 */
      handleImportXls(){
        this.$refs.importModal.show()
      },
      getImportUrl(){
        return '/electronregular/electronChannelOrderRegular/importExcel/'
      }
    }
  }
</script>
<style scoped lang="less">
  @import '~@assets/less/common.less';

</style>