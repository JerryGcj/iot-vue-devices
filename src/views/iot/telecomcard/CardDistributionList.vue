<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <a-col v-has="'cardDistributionList:selectCustomerId'" :md="6" :sm="8">
            <a-form-item label="客户">
              <!--<a-input placeholder="请输入客户" v-model="queryParam.userNameId"></a-input>-->
              <!--异步树加载组件 通过传入表名 显示字段 存储字段 加载一个树控件
              <j-tree-select dict="aa_tree_test,aad,id" pid-field="pid" ></j-tree-select>-->
              <j-tree-select
                v-model="queryParam.operationByUuid"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                condition='{"del_flag":"0"}'
                pidField="create_by_id"
                pidValue=""/>
            </a-form-item>
          </a-col>

          <a-col v-has="'cardDistributionList:selectCustomerId'" :md="6" :sm="8">
            <a-form-item label="客户名称">
              <a-input placeholder="请输入客户名称" v-model="queryParam.operationByName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="10">
            <a-form-item label="卡号区间">
              <a-input placeholder="起始卡号" class="query-group-cust" v-model="queryParam.start"></a-input>
              <span class="query-group-split-cust"></span>
              <a-input placeholder="截止卡号" class="query-group-cust" v-model="queryParam.end"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="10">
            <a-form-item label="分配时间" >
              <j-date v-model="queryParam.startTime" date-format="YYYY-MM-DD 00:00:00" style="width:45%" placeholder="请选择开始时间" ></j-date>
              <span style="width: 10px;">~</span>
              <j-date v-model="queryParam.endTime"  date-format="YYYY-MM-DD 23:59:59" style="width:45%" placeholder="请选择结束时间"></j-date>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="备注">
              <a-input placeholder="请输入备注" v-model="queryParam.note"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button type="primary" icon="download" @click="handleExportXls('分配批次查询')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
      </a-upload>
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
        @change="handleTableChange">

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
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <cardDistribution-modal ref="modalForm" @ok="modalFormOk"></cardDistribution-modal>
  </a-card>
</template>

<script>
  import CardDistributionModal from './modules/CardDistributionModal'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import JDate from '@/components/jeecg/JDate'
  export default {
    name: "CardDistributionList",
    mixins:[JeecgListMixin],
    components: {
      CardDistributionModal,
      JTreeSelect,
      JDate
    },
    data () {
      return {
        description: '分配批次查询管理页面',
        // 表头
        columns: [
		      {
            title: '代理商',
            align:"center",
            dataIndex: 'lowerAgentName'
           },
          {
            title: '代理商号码',
            align:"center",
            dataIndex: 'agentPhone',
            width: 100
          },
          {
            title: '用户等级',
            align:"center",
            dataIndex: 'levelId_dictText'
          },
          {
            title: '运营商类型',
            align:"center",
            dataIndex: 'operatorType_dictText'
          },
          {
            title: '起始卡号',
            align:"center",
            dataIndex: 'start'
          },
          {
            title: '截止卡号',
            align:"center",
            dataIndex: 'end'
          },
		      {
            title: '总卡数',
            align:"center",
            dataIndex: 'cardNumber'
           },
          {
            title: '备注',
            align:"center",
            dataIndex: 'note'
          },
          {
            title: '分配来源',
            align:"center",
            dataIndex: 'distributionType',
            customRender:function (text) {
              if(text=='1'){
                return "平台";
              }else if(text=="2"){
                return "客户端";
              } else {
                return text;
              }
            }
          },
          {
            title: '操作人',
            align:"center",
            dataIndex: 'operationByName'
          },
          {
            title: '分配时间',
            align:"center",
            dataIndex: 'createTime',
            width: 200
          }
        ],
		url: {
          list: "/carddistribution/cardDistribution/list",
          delete: "/carddistribution/cardDistribution/delete",
          deleteBatch: "/carddistribution/cardDistribution/deleteBatch",
          exportXlsUrl: "carddistribution/cardDistribution/exportXls",
          importExcelUrl: "carddistribution/cardDistribution/importExcel",
       },
    }
  },
  computed: {
    importExcelUrl: function(){
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
    methods: {

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>