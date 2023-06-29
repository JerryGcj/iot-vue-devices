<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="客户手机号">
              <a-input placeholder="请输入客户手机号" v-model="queryParam.mobile"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="iccid">
              <a-input placeholder="请输入iccid" v-model="queryParam.iccid"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="解决状态">
              <j-dict-select-tag placeholder="请选择解决状态"  v-model="queryParam.solveStatus" dict-code="solve_status"></j-dict-select-tag>
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
    <!--<div class="table-operator">-->
      <!--&lt;!&ndash;<a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>&ndash;&gt;-->
      <!--&lt;!&ndash;<a-button type="primary" icon="download" @click="handleExportXls('iot_consumer_service_record')">导出</a-button>&ndash;&gt;-->
      <!--&lt;!&ndash;<a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">&ndash;&gt;-->
        <!--&lt;!&ndash;<a-button type="primary" icon="import">导入</a-button>&ndash;&gt;-->
      <!--&lt;!&ndash;</a-upload>&ndash;&gt;-->
      <!--<a-dropdown v-if="selectedRowKeys.length > 0">-->
        <!--<a-menu slot="overlay">-->
          <!--<a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>-->
        <!--</a-menu>-->
        <!--<a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>-->
      <!--</a-dropdown>-->
    <!--</div>-->

    <!-- table区域-begin -->
    <div>
      <!--<div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">-->
        <!--<i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项-->
        <!--<a style="margin-left: 24px" @click="onClearSelected">清空</a>-->
      <!--</div>-->

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"

        @change="handleTableChange">

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
          <a @click="handleEdit(record)">处理</a>

          <!--<a-divider type="vertical" />-->
          <!--<a-dropdown>-->
            <!--<a class="ant-dropdown-link">更多 <a-icon type="down" /></a>-->
            <!--<a-menu slot="overlay">-->
              <!--<a-menu-item>-->
                <!--<a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">-->
                  <!--<a>删除</a>-->
                <!--</a-popconfirm>-->
              <!--</a-menu-item>-->
            <!--</a-menu>-->
          <!--</a-dropdown>-->
        </span>

      </a-table>
    </div>

    <iotConsumerServiceRecord-modal ref="modalForm" @ok="modalFormOk"></iotConsumerServiceRecord-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotConsumerServiceRecordModal from './modules/IotConsumerServiceRecordModal'

  export default {
    name: "IotConsumerServiceRecordList",
    mixins:[JeecgListMixin],
    components: {
      IotConsumerServiceRecordModal
    },
    data () {
      return {
        queryParam:{
          solveStatus:"0",
        },
        description: 'iot_consumer_service_record',
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
            title:'用户手机号',
            align:"center",
            dataIndex: 'mobile'
          },
          {
            title:'iccid',
            align:"center",
            dataIndex: 'iccid'
          },
          {
            title:'接入号',
            align:"center",
            dataIndex: 'accessNumber'
          },
          // {
          //   title:'短号',
          //   align:"center",
          //   dataIndex: 'virtualIccid'
          // },

          {
            title:'提问内容',
            align:"center",
            dataIndex: 'content'
          },
          {
            title:'快捷提问',
            align:"center",
            dataIndex: 'tagName'
          },
          {
            title:'提问时间',
            align:"center",
            dataIndex: 'createTime',
          },
          {
            title:'解决状态',
            align:"center",
            dataIndex: 'solveStatus_dictText'
          },
          {
            title:'解决时间',
            align:"center",
            dataIndex: 'updateTime',
          },
          {
            title:'解决人',
            align:"center",
            dataIndex: 'updateBy'
          },
          {
            title:'解决备注',
            align:"center",
            dataIndex: 'solveRemark'
          },

          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/consumer/iotConsumerServiceRecord/myList",
          delete: "/consumer/iotConsumerServiceRecord/delete",
          deleteBatch: "/consumer/iotConsumerServiceRecord/deleteBatch",
          exportXlsUrl: "/consumer/iotConsumerServiceRecord/exportXls",
          importExcelUrl: "consumer/iotConsumerServiceRecord/importExcel",
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
      }

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>