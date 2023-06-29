<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->
    
    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('electron_share_profits_history')">导出</a-button>
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

    <electronShareProfitsHistory-modal ref="modalForm" @ok="modalFormOk"></electronShareProfitsHistory-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronShareProfitsHistoryModal from './modules/ElectronShareProfitsHistoryModal'

  export default {
    name: "ElectronShareProfitsHistoryList",
    mixins:[JeecgListMixin],
    components: {
      ElectronShareProfitsHistoryModal
    },
    data () {
      return {
        description: 'electron_share_profits_history管理页面',
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
            title:'创建日期',
            align:"center",
            dataIndex: 'createTime',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'创建者',
            align:"center",
            dataIndex: 'createUser'
          },
          {
            title:'结算标识（0：我方给一级代理结算，1：一级代理给其代理结算）',
            align:"center",
            dataIndex: 'flag'
          },
          {
            title:'已分润金额(元)',
            align:"center",
            dataIndex: 'hasMoney'
          },
          {
            title:'未分润金额(元)',
            align:"center",
            dataIndex: 'noMoney'
          },
          {
            title:'运营商类型（1：移动 2：联通 3：电信）',
            align:"center",
            dataIndex: 'operatorType'
          },
          {
            title:'分润金额(元)',
            align:"center",
            dataIndex: 'shareMoney'
          },
          {
            title:'一级代理给其下代理的结算状态(0：未分润，1：已分润)',
            align:"center",
            dataIndex: 'status'
          },
          {
            title:'分润区间',
            align:"center",
            dataIndex: 'updateTime'
          },
          {
            title:'更新者',
            align:"center",
            dataIndex: 'updateUser'
          },
          {
            title:'用户ID',
            align:"center",
            dataIndex: 'userId'
          },
          {
            title:'打款方式（1：线下打款；2:公众号提现）',
            align:"center",
            dataIndex: 'withdrawMethod'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/electronshareprofitshistory/electronShareProfitsHistory/list",
          delete: "/electronshareprofitshistory/electronShareProfitsHistory/delete",
          deleteBatch: "/electronshareprofitshistory/electronShareProfitsHistory/deleteBatch",
          exportXlsUrl: "/electronshareprofitshistory/electronShareProfitsHistory/exportXls",
          importExcelUrl: "electronshareprofitshistory/electronShareProfitsHistory/importExcel",
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