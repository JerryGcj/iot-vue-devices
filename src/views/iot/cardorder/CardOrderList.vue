<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col v-has="'orderList:selectCustomerId'" :md="5" :sm="6">
            <a-form-item label="客户" >
              <j-tree-select
                v-model="queryParam.userId"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                condition='{"del_flag":"0"}'
                pidField="create_by_id"
                pidValue=""/>
            </a-form-item>
          </a-col>

          <a-col v-has="'orderList:selectCustomerIdDls'" :md="5" :sm="6">
            <a-form-item label="客户">
              <a-select v-model="queryParam.userId"
                        placeholder="请选择客户">
                <a-select-option v-for="d in userData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="6">
            <a-form-item label="ICCID">
              <a-input placeholder="请输入ICCID" v-model="queryParam.iccid"></a-input>
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
            <a-form-item label="支付状态">
              <j-dict-select-tag v-model="queryParam.payState" placeholder="请选择支付状态" dictCode="pay_state"/>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="订单状态">
              <j-dict-select-tag placeholder="请选择订单状态" v-model="queryParam.orderState" dict-code="order_status"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="支付方式">
              <a-select v-model="queryParam.wxTransactionId" placeholder="请选择支付方式" :allowClear="true">
                <a-select-option value="1">钱包支付</a-select-option>
                <a-select-option value="2">现金支付</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="订单类型">
              <j-dict-select-tag placeholder="请选择订单类型" v-model="queryParam.orderType" dict-code="order_type"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="10">
            <a-form-item label="购买时间">
              <j-date v-model="queryParam.createTime_begin" date-format="YYYY-MM-DD 00:00:00" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
              <span style="width: 10px;">~</span>
              <j-date v-model="queryParam.createTime_end" date-format="YYYY-MM-DD 23:59:59" class="query-group-cust" placeholder="请选择结束时间"></j-date>
            </a-form-item>
          </a-col>
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
      <a-button type="primary" icon="download" @click="handleExportXls('联通套餐充值记录')">导出</a-button>
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

        <span slot="id" slot-scope="text">
          <j-ellipsis :value="text" :length="18" />
        </span>
        <span slot="wxTransactionId" slot-scope="text">
          <j-ellipsis :value="text" :length="18" />
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
    <card-order-import-modal ref="importModal" :url="getImportUrl()" @ok="importOk"></card-order-import-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotCardOrderModal from './modules/IotCardOrderModal'
  import JDate from '@/components/jeecg/JDate'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import CardOrderImportModal from './modules/CardOrderImportModal'
  import {queryLowerAgent} from '@/api/api'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import JEllipsis from "@/components/jeecg/JEllipsis"

  export default {
    name: "CardOrderList",
    mixins:[JeecgListMixin],
    components: {
      IotCardOrderModal,
      JDate,
      JSearchSelectTag,
      CardOrderImportModal,
      JTreeSelect,
      JEllipsis
    },
    data () {
      return {
        description: 'card_order管理页面',
        userData:[],
        // 表头
        columns: [
          {
            title:'ICCID',
            align:"center",
            dataIndex: 'iccid'
          },
          {
            title:'客户手机号',
            align:"center",
            dataIndex: 'cusPhone'
          },
          {
            title:'支付状态',
            align:"center",
            dataIndex: 'payState',
            scopedSlots: { customRender: 'PayState' }
          },
          {
            title:'订单状态',
            align:"center",
            dataIndex: 'orderState_dictText'
          },
          {
            title:'套餐名称',
            align:"center",
            dataIndex: 'packageName'
          },
          {
            title:'支付金额(元)',
            align:"center",
            dataIndex: 'orderPayPrice'
          },
          {
            title:'购买时间',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title:'商户单号',
            align:"center",
            dataIndex: 'id',
            scopedSlots: {customRender: 'id'}
          },
          {
            title:'交易单号',
            align:"center",
            dataIndex: 'wxTransactionId',
            scopedSlots: {customRender: 'wxTransactionId'}
          },
          {
            title:'支付方式',
            align:"center",
            dataIndex: 'wxTransactionId',
            customRender:function (text) {
              if(text==null){
                return "钱包支付";
              }else{
                return "现金支付";
              }
            }
          },
          {
            title:'订单类型',
            align:"center",
            dataIndex: 'orderType_dictText'
          },
          {
            title:'企业名称',
            align:"center",
            dataIndex: 'userCompany'
          }
        ],
        url: {
          list: "/cardorder/iotCardOrder/orderlist",
          delete: "/cardorder/iotCardOrder/delete",
          deleteBatch: "/cardorder/iotCardOrder/deleteBatch",
          exportXlsUrl: "/cardorder/iotCardOrder/exportExls",
          importExcelUrl: "cardorder/iotCardOrder/importExcel",
        },
        dictOptions:{
        },
        queryParam: {
          createTime_begin: this.dateFormat(new Date()),
          createTime_end: this.dateFormat(new Date()),
          payState:"4"
        }
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    mounted() {
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
    },
    methods: {
      initDictConfig(){
      },
      getImportUrl(){
        return '/cardorder/iotCardOrder/importExcel/'
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>