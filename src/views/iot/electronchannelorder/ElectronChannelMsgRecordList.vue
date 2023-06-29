<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="导入批次">
              <a-select  placeholder="请选择导入批次" v-model="queryParam.sendBatch" :allowClear=true >
                <a-select-option v-for="(item,i) in importBatch" :value="item">
                  {{item}}
                </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
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
    </div>

    <!-- table区域-begin -->
    <div>
<!--      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">-->
<!--        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项-->
<!--        <a style="margin-left: 24px" @click="onClearSelected">清空</a>-->
<!--      </div>-->

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

      </a-table>
    </div>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronChannelOrderModal from './modules/ElectronChannelOrderModal'
  import ElectronSendSmsModal from './modules/ElectronSendSmsModal'
  import { postAction,getAction,downFile} from '@/api/manage'
  import ElectronGraphReportModal from "./modules/ElectronGraphReportModal";

  export default {
    name: "ElectronChannelOrderList",
    mixins:[JeecgListMixin],
    components: {
      ElectronGraphReportModal,
      ElectronChannelOrderModal,
      ElectronSendSmsModal
    },
    data () {
      return {
        description: 'electron_channel_order管理页面',
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
            title:'发送批次',
            align:"center",
            dataIndex: 'sendBatch'
          },
          {
            title:'发送内容',
            align:"center",
            dataIndex: 'sendContext'
          },
          {
            title:'发送条数',
            align:"center",
            dataIndex: 'sendCount'
          },
          {
            title:'发送时间',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title:'发送人',
            align:"center",
            dataIndex: 'createBy'
          },
          {
            title:'更新时间',
            align:"center",
            dataIndex: 'updateTime'
          },

        ],
        url: {
          list: "/electronchannelorder/electronChannelMsgRecord/list"
        },
        dictOptions:{
        },
        importBatch:[],
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
      syncExpressNoAndIccid: function(){
        return `${window._CONFIG['domianURL']}/${this.url.syncExpressNoAndIccid}`;
      },
      accessNumberUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.accessNumberUrl}`;
      }
    },
    created() {
      this.queryImportBatch();
    },
    methods: {
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>