<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="运营商">
              <a-select :dropdownStyle="{ maxHeight: '200px', overflow: 'auto' }"
                        v-decorator="[ 'operatorId', {}]"
                        v-model="queryParam.operatorId"
                        placeholder="请选择运营商">
                <a-select-option v-for="d in operatorData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="备注">
              <a-input placeholder="请输入备注" v-model="queryParam.note"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="10" :sm="12">
            <a-form-item label="创建时间" :labelCol="{span: 5}" :wrapperCol="{span: 18, offset: 1}">
              <j-date v-model="queryParam.startTime" :showTime="true" date-format="YYYY-MM-DD HH:mm:ss" style="width:45%" placeholder="请选择开始时间" ></j-date>
              <span style="width: 10px;">~</span>
              <j-date v-model="queryParam.endTime" :showTime="true" date-format="YYYY-MM-DD HH:mm:ss" style="width:45%" placeholder="请选择结束时间"></j-date>
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
      <a-button type="primary" icon="download" @click="handleExportXls('导入批次查询')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
      </a-upload>
      <!--<a-dropdown v-if="selectedRowKeys.length > 0">-->
        <!--<a-menu slot="overlay">-->
          <!--<a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>-->
        <!--</a-menu>-->
        <!--<a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>-->
      <!--</a-dropdown>-->
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
    <importBatch-modal ref="modalForm" @ok="modalFormOk"></importBatch-modal>
  </a-card>
</template>

<script>
  import ImportBatchModal from './modules/ImportBatchModal'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import {queryOperator } from '@/api/api'
  import JDate from '@/components/jeecg/JDate'

  export default {
    name: "ImportBatchList",
    mixins:[JeecgListMixin],
    components: {
      ImportBatchModal,
      queryOperator,
      JDate
    },
    data () {
      return {
        description: '导入批次查询管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            operatorData:[],
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
           },
		   {
            title: '分配客户',
            align:"center",
            dataIndex: 'operationByName'
           },

          {
            title: '运营商',
            align:"center",
            dataIndex: 'operatorName'
          },
          {
            title: '类型 ',
            align:"center",
            dataIndex: 'importType',
            customRender:function (text) {
              if(text=='1'){
                return "execl表格";
              }else if(text=="2"){
                return "iccid区间";
              }else if(text=="3"){
                return "msisdn区间";
              } else {
                return text;
              }
            },
            width: 100
          },
          {
            title: 'iccid区间',
            align:"center",
            dataIndex: 'importInterval',
            width: 300
          },
          {
            title: '总条数',
            align:"center",
            dataIndex: 'cardNumber'
          },
          {
            title: '操作人',
            align:"center",
            dataIndex: 'higherAgentName'
          },
          {
            title: '备注',
            align:"center",
            dataIndex: 'note'
          },

          {
            title: '创建时间',
            align:"center",
            dataIndex: 'createTime',
            width: 200
          }
        ],
		url: {
          list: "/importbatch/importBatch/list",
          delete: "/importbatch/importBatch/delete",
          deleteBatch: "/importbatch/importBatch/deleteBatch",
          exportXlsUrl: "importbatch/importBatch/exportXls",
          importExcelUrl: "importbatch/importBatch/importExcel",
       },
    }
  },
  computed: {
    importExcelUrl: function(){

      var that = this;
      queryOperator().then((res)=>{
        if(res.success){
        that.operatorData = [];
        let treeList = res.result
        for(let a=0;a<treeList.length;a++){
          let temp = treeList[a];
          this.operatorData.push({
            value:temp.id,
            text:temp.operatorName
          })
        }
      }
    });


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