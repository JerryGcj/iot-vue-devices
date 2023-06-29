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
            <a-form-item label="接入号">
              <a-input placeholder="请输入接入号" v-model="queryParam.accessNumber"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="4">
            <a-form-item label="入网月">
              <a-month-picker placeholder="请选择月份" v-model="queryParam.activateDate" />
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="4">
            <a-form-item label="佣金月">
              <a-month-picker placeholder="请选择月份" v-model="queryParam.commissionDate" />
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
<!--      <a-button type="primary" icon="download" @click="handleExportXls('electron_operation_income')">导出</a-button>-->
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

    <electronOperationIncome-modal ref="modalForm" @ok="modalFormOk"></electronOperationIncome-modal>
    <electron-operation-income-import ref="electronOperationIncomeImport" @ok="modalFormOk"></electron-operation-income-import>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronOperationIncomeModal from './modules/ElectronOperationIncomeModal'
  import ElectronOperationIncomeImport from './modules/ElectronOperationIncomeImport'

  export default {
    name: "ElectronOperationIncomeList",
    mixins:[JeecgListMixin],
    components: {
      ElectronOperationIncomeModal,
      ElectronOperationIncomeImport
    },
    data () {
      return {
        description: 'electron_operation_income管理页面',
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
            title:'接入号',
            align:"center",
            dataIndex: 'accessNumber'
          },
          {
            title:'基础套餐价格',
            align:"center",
            dataIndex: 'standardPrice'
          },
          {
            title:'首充奖励折扣比',
            align:"center",
            dataIndex: 'firstRewardDiscount'
          },
          {
            title:'分佣比',
            align:"center",
            dataIndex: 'commissionRatio'
          },
          {
            title:'佣金',
            align:"center",
            dataIndex: 'commission'
          },
          {
            title:'入网月份',
            align:"center",
            dataIndex: 'activateDate'
          },
          {
            title:'佣金月份',
            align:"center",
            dataIndex: 'commissionDate'
          },
          {
            title:'导入日期',
            align:"center",
            dataIndex: 'createTime'
          },
          /*{
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }*/
        ],
        url: {
          list: "/electronoperationincome/electronOperationIncome/list",
          delete: "/electronoperationincome/electronOperationIncome/delete",
          deleteBatch: "/electronoperationincome/electronOperationIncome/deleteBatch",
          exportXlsUrl: "/electronoperationincome/electronOperationIncome/exportXls",
          importExcelUrl: "electronoperationincome/electronOperationIncome/importExcel",
        },
        dictOptions:{
        },
        /* 排序参数 */
        isorter:{
          column: 'commissionDate',
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
        this.$refs.electronOperationIncomeImport.add();
        this.$refs.electronOperationIncomeImport.title = "佣金导入";
        this.$refs.electronOperationIncomeImport.disableSubmit = false;
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>