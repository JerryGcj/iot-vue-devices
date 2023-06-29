<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="7" :sm="9">
            <a-form-item label="iccid">
              <a-input placeholder="请输入iccid" class="query-group-cust" v-model="queryParam.iccid"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="msisdn">
              <a-input placeholder="请输入msisdn" class="query-group-cust" v-model="queryParam.msisdn"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="停机状态码">
              <j-dict-select-tag placeholder="请选择停机状态码" v-model="queryParam.stopReason"  dictCode="stop_reason"/>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">

          </template>
          <a-col :md="6" :sm="8" >
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
      <a-button @click="updataNowStatus" type="primary" icon="refresh">更新最新状态</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('iot_card_separate')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <!--<a-button type="primary" icon="import">导入</a-button>-->
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <!--<a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>-->
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

    <iotCardSeparate-modal ref="modalForm" @ok="modalFormOk"></iotCardSeparate-modal>
  </a-card>
</template>

<script>
  import { deleteAction,getAction,downFile} from '@/api/manage'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotCardSeparateModal from './modules/IotCardSeparateModal'
  import JDictSelectTag from '@/components/dict/JDictSelectTag.vue'
  import {initDictOptions, filterMultiDictText} from '@/components/dict/JDictSelectUtil'

  export default {
    name: "IotCardSeparateList",
    mixins:[JeecgListMixin],
    components: {
      JDictSelectTag,
      IotCardSeparateModal
    },
    data () {
      return {
        description: 'iot_card_separate管理页面',
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
            title:'iccid',
            align:"center",
            dataIndex: 'iccid'
          },
          {
            title:'msisdn',
            align:"center",
            dataIndex: 'msisdn'
          },
          {
            title:'停机状态码',
            align:"center",
            dataIndex: 'stopReason_dictText',
          },
          {
            title:'插入时间',
            align:"center",
            dataIndex: 'createTime'
          }
        ],
        url: {
          list: "/cardSeparate/iotCardSeparate/list",
          delete: "/cardSeparate/iotCardSeparate/delete",
          deleteBatch: "/cardSeparate/iotCardSeparate/deleteBatch",
          exportXlsUrl: "/cardSeparate/iotCardSeparate/exportXls",
          importExcelUrl: "cardSeparate/iotCardSeparate/importExcel",
        },
        dictOptions:{
         stopReason:[],
        },
        isorter:{
          column: 'stop_reason',
          order: 'desc',
        },
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {

      updataNowStatus(){
        getAction('/cardSeparate/iotCardSeparate/syncCardStopReason',null).then((res) => {
          if (res.success) {
            this.$message.success(res.result)
          }else{
            this.$message.warning(res.message)
          }
        })
      },
       
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>