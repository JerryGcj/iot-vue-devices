<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6">
            <a-form-item label="运营商">
              <j-dict-select-tag v-model="queryParam.operatorId" placeholder="请选择运营商" dict-code="electron_operator_config,operator,id"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="结佣账号">
              <a-input placeholder="请输入结佣账号" v-model="queryParam.cusName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="接入号">
              <a-input placeholder="请输入接入号" v-model="queryParam.accessNumber"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="4">
            <a-form-item label="入网月">
              <a-month-picker placeholder="请选择月份" v-model="queryParam.activateMonth" />
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="用户类型">
              <a-select allowClear="true" placeholder="请选择用户类型" v-model="queryParam.cusType">
                <a-select-option value="0">渠道</a-select-option>
                <a-select-option value="1">代理</a-select-option>
              </a-select>
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
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
<!--      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>-->
<!--      <a-button type="primary" icon="download" @click="handleExportXls('佣金支出')">导出</a-button>-->
      <a-button type="primary" icon="import" @click="handleImportExcel">导入</a-button>
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

<!--        <span slot="action" slot-scope="text, record">-->
<!--          <a @click="handleEdit(record)">编辑</a>-->

<!--          <a-divider type="vertical" />-->
<!--          <a-dropdown>-->
<!--            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>-->
<!--            <a-menu slot="overlay">-->
<!--              <a-menu-item>-->
<!--                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">-->
<!--                  <a>删除</a>-->
<!--                </a-popconfirm>-->
<!--              </a-menu-item>-->
<!--            </a-menu>-->
<!--          </a-dropdown>-->
<!--        </span>-->

      </a-table>
    </div>

    <electronOperationCommissionExpenses-modal ref="modalForm" @ok="modalFormOk"></electronOperationCommissionExpenses-modal>
    <electron-operation-commission-expenses-import ref="electronOperationCommissionExpensesImport" @ok="modalFormOk"></electron-operation-commission-expenses-import>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronOperationCommissionExpensesModal from './modules/ElectronOperationCommissionExpensesModal'
  import ElectronOperationCommissionExpensesImport from './modules/ElectronOperationCommissionExpensesImport'

  export default {
    name: "ElectronOperationCommissionExpensesList",
    mixins:[JeecgListMixin],
    components: {
      ElectronOperationCommissionExpensesImport,
      ElectronOperationCommissionExpensesModal
    },
    data () {
      return {
        description: '佣金支出管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'运营商',
            align:"center",
            dataIndex: 'operatorId_dictText'
          },
          {
            title:'结佣账号',
            align:"center",
            dataIndex: 'cusName'
          },
          {
            title:'激活月份（入网月）',
            align:"center",
            dataIndex: 'activateMonth'
          },
          {
            title:'接入号',
            align:"center",
            dataIndex: 'accessNumber'
          },
          {
            title:'用户类型',
            align:"center",
            dataIndex: 'cusType',
            customRender: function (text) {
              return text == "0" ? "渠道" : (text == "1" ? "代理" : "未知")
            }
          },
          {
            title:'结佣规则',
            align:"center",
            dataIndex: 'commissionPolicy',
            customRender: function (text) {
              if(text<=1){
                return "抽成比："+text*100+"%"
              }else{
                return "一次性返佣："+text+"元"
              }
            }
          },
          {
            title:'导入日期',
            align:"center",
            dataIndex: 'createTime'
          },
          // {
          //   title: '操作',
          //   dataIndex: 'action',
          //   align:"center",
          //   scopedSlots: { customRender: 'action' }
          // }
        ],
        url: {
          list: "/electronoperationcommissionexpenses/electronOperationCommissionExpenses/list",
          delete: "/electronoperationcommissionexpenses/electronOperationCommissionExpenses/delete",
          deleteBatch: "/electronoperationcommissionexpenses/electronOperationCommissionExpenses/deleteBatch",
          exportXlsUrl: "/electronoperationcommissionexpenses/electronOperationCommissionExpenses/exportXls",
          importExcelUrl: "/electronoperationcommissionexpenses/electronOperationCommissionExpenses/importExcel",
        },
        dictOptions:{
        },
        /* 排序参数 */
        isorter:{
          column: 'activateMonth',
          order: 'desc',
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
      handleImportExcel: function () {
        this.$refs.electronOperationCommissionExpensesImport.add();
        this.$refs.electronOperationCommissionExpensesImport.title = "佣金支出导入";
        this.$refs.electronOperationCommissionExpensesImport.disableSubmit = false;
      }

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>