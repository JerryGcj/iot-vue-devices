<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6">
            <a-form-item label="openid">
              <a-input placeholder="请输入openid" v-model="queryParam.openid"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="旧卡号">
              <a-input placeholder="请输入旧卡号" v-model="queryParam.oldIccid"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="新卡号">
              <a-input placeholder="请输入新卡号" v-model="queryParam.newIccid"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="旧手机号">
              <a-input placeholder="请输入旧手机号" v-model="queryParam.oldMobile"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="新手机号">
              <a-input placeholder="请输入新手机号" v-model="queryParam.newMobile"></a-input>
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
    <!-- 查询区域-END -->

    <!-- 操作按钮区域
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('change_card_record')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div> -->

    <!-- table区域-begin -->
    <div>

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

    <changeCardRecord-modal ref="modalForm" @ok="modalFormOk"></changeCardRecord-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ChangeCardRecordModal from './modules/ChangeCardRecordModal'

  export default {
    name: "ChangeCardRecordList",
    mixins:[JeecgListMixin],
    components: {
      ChangeCardRecordModal
    },
    data () {
      return {
        description: 'change_card_record管理页面',
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
            title:'openid',
            align:"center",
            dataIndex: 'openid'
          },
          {
            title:'旧卡号',
            align:"center",
            dataIndex: 'oldIccid'
          },
          {
            title:'新卡号',
            align:"center",
            dataIndex: 'newIccid'
          },
          {
            title:'旧手机号',
            align:"center",
            dataIndex: 'oldMobile'
          },
          {
            title:'新手机号',
            align:"center",
            dataIndex: 'newMobile'
          },
          {
            title:'转移金额',
            align:"center",
            dataIndex: 'beforeBalance'
          },
          {
            title:'操作类型',
            align:"center",
            dataIndex: 'operationType',
            customRender:function (text) {
              if(text=="0"){
                return "换卡";
              }else if(text=="1"){
                return "转移余额";
              }
            }
          },
          {
            title:'操作人',
            align:"center",
            dataIndex: 'createUser'
          },
          {
            title:'操作时间',
            align:"center",
            dataIndex: 'createTime'
          }
        ],
        url: {
          list: "/changecardrecord/changeCardRecord/list",
          delete: "/changecardrecord/changeCardRecord/delete",
          deleteBatch: "/changecardrecord/changeCardRecord/deleteBatch",
          exportXlsUrl: "/changecardrecord/changeCardRecord/exportXls",
          importExcelUrl: "changecardrecord/changeCardRecord/importExcel",
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