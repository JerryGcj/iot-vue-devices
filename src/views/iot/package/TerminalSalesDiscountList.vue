<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col v-has="'cardInformationList:selectCustomerId'" :md="4" :sm="6">
            <a-form-item label="下级代理" >
              <j-tree-select
                v-model="queryParam.userId"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                pidField="create_by_id"
                pidValue=""/>
            </a-form-item>
          </a-col>

          <a-col v-has="'cardInformationList:selectCustomerIdDls'" :md="4" :sm="6">
            <a-form-item label="下级代理">
              <a-select v-model="queryParam.userId"
                        placeholder="客户">
                <a-select-option v-for="d in userData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

          <a-col :md="4" :sm="6">
            <a-form-item label="运营商类型">
              <j-dict-select-tag placeholder="请选择运营商类型" v-model="queryParam.operatorType" dict-code="operator_type"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="代理商登录名">
              <a-input placeholder="请输入代理商登录名" v-model="queryParam.createUser"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="4" :sm="6">
            <a-form-item label="套餐名称">
              <a-input placeholder="请输入套餐名称" v-model="queryParam.packageName"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="4" :sm="6">
            <a-form-item label="套餐状态">
              <a-select v-model="queryParam.state" placeholder="请选择">
                <a-select-option value="0">上架</a-select-option>
                <a-select-option value="1">下架</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

          <a-col :md="4" :sm="6" >
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
      <a-button v-has="'terminalSalesDiscountList:add'" @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="" @click="handleCardFlowUsageRatio">自定义用量倍数</a-button>
      <!--<a-button type="primary" icon="download" @click="handleExportXls('终端销售折扣管理')">导出</a-button>-->
      <!--<a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
        <!--<a-button type="primary" icon="import">导入</a-button>-->
      <!--</a-upload>-->
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
          <a v-has="'terminalSalesDiscountList:edit'" @click="terminalEdit(record)">编辑<a-divider type="vertical" /></a>

          <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
            <a v-has="'terminalSalesDiscountList:delete'">删除</a>
          </a-popconfirm>
        </span>
        <template slot="state" slot-scope="state">
          <a-tag v-if="state==0" color="green">上架</a-tag>
          <a-tag v-if="state==1" color="red">下架</a-tag>
        </template>
      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <terminalSalesDiscount-modal ref="modalForm" @ok="modalFormOk"></terminalSalesDiscount-modal>
    <terminal-sales-discount-modal-editor ref="TerminalSalesDiscountModalEditor" @ok="modalFormOk"></terminal-sales-discount-modal-editor>
    <flow-usage-ratio ref="flowUsageRatio" @ok="modalFormOk" @hello="onClearSelected"></flow-usage-ratio>
  </a-card>
</template>

<script>
  import TerminalSalesDiscountModal from './modules/TerminalSalesDiscountModal'
  import TerminalSalesDiscountModalEditor from './modules/TerminalSalesDiscountModalEditor'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import {queryLowerAgent } from '@/api/api'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import FlowUsageRatio from './modules/FlowUsageRatio'

  export default {
    name: "TerminalSalesDiscountList",
    mixins:[JeecgListMixin],
    components: {
      TerminalSalesDiscountModal,
      TerminalSalesDiscountModalEditor,
      JTreeSelect,
      FlowUsageRatio
    },
    data () {
      return {
        description: '终端销售折扣管理管理页面',
        userData:[],
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
            title: '销售价格（元）',
            align:"center",
            dataIndex: 'salesPrice'
          },

          {
            title: '套餐名称',
            align:"center",
            dataIndex: 'packageName'
          },
          {
            title: '状态',
            align:"center",
            dataIndex: 'state',
            scopedSlots: { customRender: 'state' }
          },
          {
            title: '运营商类型',
            align:"center",
            dataIndex: 'operatorType_dictText',

          },
          {
            title: '代理商',
            align:"center",
            dataIndex: 'userCompany'
          },
          /*{
            title: '计费类型',
            align:"center",
            dataIndex: 'isNew',
            customRender:function (text) {
              if(text==0){
                return "旧资费";
              }else if(text==1){
                return "新资费";
              }else {
                return text;
              }
            }
          },*/
          {
            title: '自定义倍数',
            align:"center",
            dataIndex: 'customPackageUse'
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
          list: "/terminalsalesdiscount/terminalSalesDiscount/list",
          delete: "/terminalsalesdiscount/terminalSalesDiscount/delete",
          deleteBatch: "/terminalsalesdiscount/terminalSalesDiscount/deleteBatch",
          exportXlsUrl: "terminalsalesdiscount/terminalSalesDiscount/exportXls",
          importExcelUrl: "terminalsalesdiscount/terminalSalesDiscount/importExcel",
       },
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

      terminalEdit: function (record) {
        this.$refs.TerminalSalesDiscountModalEditor.edit(record);
        this.$refs.TerminalSalesDiscountModalEditor.title = "编辑";
        this.$refs.TerminalSalesDiscountModalEditor.disableSubmit = false;
      },
      handleCardFlowUsageRatio: function () {
        if(this.selectedRowKeys.length<1){
          this.$message.warning('请选择一个套餐！');
          return;
        }
        this.$refs.flowUsageRatio.add(this.selectedRowKeys);
        this.$refs.flowUsageRatio.title = "流量使用比例";
        this.$refs.flowUsageRatio.disableSubmit = false;
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>