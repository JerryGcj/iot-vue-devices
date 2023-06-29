<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="用户名">
              <a-input placeholder="请输入用户名" v-model="queryParam.userName"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="上级代理用户名">
              <a-input placeholder="请输入上级代理用户名" v-model="queryParam.higherAgentName"></a-input>
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
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('代理商列表')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
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
    <agent-modal ref="modalForm" @ok="modalFormOk"></agent-modal>
  </a-card>
</template>

<script>
  import AgentModal from './modules/AgentModal'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  export default {
    name: "AgentList",
    mixins:[JeecgListMixin],
    components: {
      AgentModal
    },
    data () {
      return {
        description: '代理商列表管理页面',
        // 表头
        columns: [
		      {
            title: '用户名',
            align:"center",
            dataIndex: 'userName'
           },

          {
            title: '上级代理用户名',
            align:"center",
            dataIndex: 'higherAgentName'
          },
          {
            title: '状态',
            align:"center",
            dataIndex: 'state',
            customRender:function (text) {
              if(text=='0'){
                return "可用";
              }else if(text=="1"){
                return "禁用";
              } else {
                return text;
              }
            }
          },
          {
            title: '预存金额',
            align:"center",
            dataIndex: 'amountDeposited'
          },

          {
            title: '是否可以开下级代理',
            align:"center",
            dataIndex: 'openAgent',
            customRender:function (text) {
              if(text=='0'){
                return "是";
              }else if(text=="1"){
                return "否";
              } else {
                return text;
              }
            }
          },
          {
            title: '返佣类型',
            align:"center",
            dataIndex: 'commissionType',
            customRender:function (text) {
              if(text=='0'){
                return "平台返佣金";
              }else if(text=="1"){
                return "全额代理返佣";
              }else if(text=="2"){
                return "上级代理返佣";
              } else {
                return text;
              }
            }
          },
          {
            title: '创建时间',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
		url: {
          list: "/agent/agent/list",
          delete: "/agent/agent/delete",
          deleteBatch: "/agent/agent/deleteBatch",
          exportXlsUrl: "agent/agent/exportXls",
          importExcelUrl: "agent/agent/importExcel",
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