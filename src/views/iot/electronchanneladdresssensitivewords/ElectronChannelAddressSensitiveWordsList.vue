<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6">
            <a-form-item label="敏感词">
              <a-input placeholder="请输入敏感词" v-model="queryParam.sensitiveWords"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="替换词">
              <a-input placeholder="请输入替换词" v-model="queryParam.replaceWords"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button @click="handleAdd" type="primary" icon="plus" style="margin-left: 8px">新增</a-button>
            </span>
          </a-col>


        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->
    
    <!-- 操作按钮区域 -->
    <div class="table-operator">
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
          <a @click="handleEdit(record)">编辑<a-divider type="vertical" /></a>
          <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
            <a>删除</a>
          </a-popconfirm>
        </span>

      </a-table>
    </div>

    <electronChannelAddressSensitiveWords-modal ref="modalForm" @ok="modalFormOk"></electronChannelAddressSensitiveWords-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronChannelAddressSensitiveWordsModal from './modules/ElectronChannelAddressSensitiveWordsModal'

  export default {
    name: "ElectronChannelAddressSensitiveWordsList",
    mixins:[JeecgListMixin],
    components: {
      ElectronChannelAddressSensitiveWordsModal
    },
    data () {
      return {
        description: 'electron_channel_address_sensitive_words管理页面',
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
            title:'敏感词',
            align:"center",
            dataIndex: 'sensitiveWords'
          },
          {
            title:'替换词',
            align:"center",
            dataIndex: 'replaceWords'
          },
          // {
          //   title:'创建者',
          //   align:"center",
          //   dataIndex: 'createBy'
          // },
          // {
          //   title:'创建时间',
          //   align:"center",
          //   dataIndex: 'createTime',
          //   customRender:function (text) {
          //     return !text?"":(text.length>10?text.substr(0,10):text)
          //   }
          // },
          // {
          //   title:'更新者',
          //   align:"center",
          //   dataIndex: 'updateBy'
          // },
          // {
          //   title:'更新时间',
          //   align:"center",
          //   dataIndex: 'updateTime',
          //   customRender:function (text) {
          //     return !text?"":(text.length>10?text.substr(0,10):text)
          //   }
          // },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/electronchanneladdresssensitivewords/electronChannelAddressSensitiveWords/list",
          delete: "/electronchanneladdresssensitivewords/electronChannelAddressSensitiveWords/delete",
          deleteBatch: "/electronchanneladdresssensitivewords/electronChannelAddressSensitiveWords/deleteBatch",
          exportXlsUrl: "/electronchanneladdresssensitivewords/electronChannelAddressSensitiveWords/exportXls",
          importExcelUrl: "electronchanneladdresssensitivewords/electronChannelAddressSensitiveWords/importExcel",
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