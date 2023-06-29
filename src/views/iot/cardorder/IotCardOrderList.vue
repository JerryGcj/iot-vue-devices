<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6">
            <a-form-item label="客户">
              <j-search-select-tag
                placeholder="请选择客户"
                v-model="queryParam.cusId"
                dict="sys_user,realname,id"
                :async="true">
              </j-search-select-tag>
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
            <a-form-item label="订单号">
              <a-input placeholder="请输入订单号" v-model="queryParam.id"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="运单号">
              <a-input placeholder="请输入运单号" v-model="queryParam.expressNo"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="支付状态">
              <j-dict-select-tag v-model="queryParam.payState" placeholder="请选择支付状态" dictCode="pay_state"/>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="8">
            <a-form-item label="发货状态">
              <j-dict-select-tag placeholder="请选择发货状态" v-model="queryParam.deliveryState" dict-code="unicom_order_state"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :md="8" :sm="10">
              <a-form-item label="提单日期">
                <j-date :show-time="true" v-model="queryParam.createTime_begin" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
                <span style="width: 10px;"> ~ </span>
                <j-date :show-time="true" v-model="queryParam.createTime_end" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择结束时间"></j-date>
              </a-form-item>
            </a-col>
          </template>
          <a-col :md="4" :sm="6" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
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
      <a-button v-has="'cardOrder:importXls'" type="primary" icon="import" @click="handleImportXls" >导入</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('联通提单记录')">导出</a-button>
      <a-button v-has="'cardOrder:updateDelivery'" type="primary" icon="import" @click="handleImportExls" >批量更新发货信息</a-button>
      <a-button v-has="'cardOrder:updateStatus'" type="primary" icon="" @click="handleDataUpdate" :loading="uploading" >更新快递状态</a-button>
      <!--<a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>-->
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
        :rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"

        @change="handleTableChange">

        <span slot="id" slot-scope="text">
          <j-ellipsis :value="text" :length="14" />
        </span>

        <template slot="PayState" slot-scope="state">
          <a-tag v-if="state==0" color="gray">未支付</a-tag>
          <a-tag v-if="state==1" color="cyan">支付退出</a-tag>
          <a-tag v-if="state==2" color="purple">支付异常</a-tag>
          <a-tag v-if="state==3" color="red">支付失败</a-tag>
          <a-tag v-if="state==4" color="green">支付成功</a-tag>
          <a-tag v-if="state==5" color="orange">退款失败</a-tag>
          <a-tag v-if="state==6" color="orange">退款成功</a-tag>
        </template>
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

        <span slot="expressNo" slot-scope="id">
          <a v-text="id" @click="showDetails(id)"></a>
        </span>
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

    <iotCardOrder-modal ref="modalForm" @ok="modalFormOk"></iotCardOrder-modal>
    <card-order-import-modal ref="updateModal" :url="getImportUrl()" @ok="importOk"></card-order-import-modal>
    <iot-card-order-import-modal ref="importModal" :url="getImportUrl()" @ok="importOk"></iot-card-order-import-modal>
    <express-details-modal ref="detailsModal" @ok="modalFormOk"></express-details-modal>
  </a-card>
</template>

<script>

  import { getAction } from '@/api/manage'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotCardOrderModal from './modules/IotCardOrderModal'
  import JDate from '@/components/jeecg/JDate'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import JEllipsis from "@/components/jeecg/JEllipsis"
  import CardOrderImportModal from './modules/CardOrderImportModal'
  import IotCardOrderImportModal from "./modules/IotCardOrderImportModal";
  import ExpressDetailsModal from "./modules/ExpressDetailsModal";

  export default {
    name: "IotCardOrderList",
    mixins:[JeecgListMixin],
    components: {
      IotCardOrderModal,
      JDate,
      JSearchSelectTag,
      JEllipsis,
      CardOrderImportModal,
      IotCardOrderImportModal,
      ExpressDetailsModal
    },
    data () {
      return {
        description: 'iot_card_order管理页面',
        uploading:false,
        // 表头
        columns: [
          {
            title:'订单号',
            align:"center",
            dataIndex: 'id',
            scopedSlots: {customRender: 'id'}
          },
          {
            title:'用户账号',
            align:"center",
            dataIndex: 'cusId_dictText'
          },
          {
            title:'客户姓名',
            align:"center",
            dataIndex: 'cusName'
          },
          {
            title:'客户手机号',
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
            align:"center",
            dataIndex: 'detailAddr'
          },
          {
            title:'代理商',
            align:"center",
            dataIndex: 'userCompany'
          },
          {
            title:'支付金额',
            align:"center",
            dataIndex: 'orderPayPrice'
          },
          {
            title:'支付状态',
            align:"center",
            dataIndex: 'payState',
            scopedSlots: { customRender: 'PayState' }
          },
          {
            title:'发货状态',
            align:"center",
            dataIndex: 'deliveryState_dictText'
          },
          {
            title:'ICCID',
            align:"center",
            dataIndex: 'iccid'
          },
          {
            title:'快递单号',
            align:"center",
            dataIndex: 'expressNo',
            scopedSlots: {customRender: 'expressNo'},
          },
          {
            title:'快递公司',
            align:"center",
            dataIndex: 'expressId_dictText'
          },
          {
            title:'快递状态',
            align:"center",
            dataIndex: 'expressState_dictText'
          },
          {
            title:'订单来源',
            align:"center",
            dataIndex: 'origin'
          },
          {
            title:'提交时间',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title:'备注',
            align:"center",
            dataIndex: 'remark'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/cardorder/iotCardOrder/list",
          delete: "/cardorder/iotCardOrder/delete",
          deleteBatch: "/cardorder/iotCardOrder/deleteBatch",
          exportXlsUrl: "/cardorder/iotCardOrder/exportXls",
          importExcelUrl: "cardorder/iotCardOrder/importExcel",
        },
        dictOptions:{
        },
        queryParam: {
          payState:"4"
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
      /* 显示导入对话框 */
      handleImportExls(){
        this.$refs.updateModal.show()
      },
      getImportUrl(){
        return '/cardorder/iotCardOrder/importExcel/'
      },
      showDetails:function(id){
        getAction('cardorder/iotCardOrder/queryByExpressNo?expressNo='+id,null).then((res) => {
          console.log(res)
          if (res.success) {
            if(null!=res.result.errorMsg&&""!=res.result.errorMsg){
              this.$message.warning(res.result.errorMsg)
            }else{
              this.$refs.detailsModal.add(res.result);
              this.$refs.detailsModal.disableSubmit = true;
            }
          }else{
            this.$message.warning(res.message)
          }
        })
      },
      handleDataUpdate: function () {
        if (this.selectedRowKeys.length != 1) {
          this.$message.warning('请选择一条记录！');
          return false;
        }else {
          this.uploading = true;
          getAction('cardorder/iotCardOrder/updateState?id='+this.selectedRowKeys,null).then((res)=>{
            if(res.success){
              this.$message.success(res.message);
              this.onClearSelected();
              this.uploading = false;
            }else{
              this.$message.warning(res.message);
              this.onClearSelected();
              this.uploading = false;
            }
          })

        }
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>