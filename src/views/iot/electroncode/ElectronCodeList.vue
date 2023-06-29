<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="市">
              <a-input placeholder="请输入市" v-model="queryParam.city"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="区">
              <a-input placeholder="请输入区" v-model="queryParam.county"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="6">
            <a-form-item label="类型">
              <a-select v-model="queryParam.type" placeholder="请选择类型" allowClear>
                <a-select-option value="jiangsu-unicom">jiangsu-unicom</a-select-option>
                <a-select-option value="taizhou-telecom">taizhou-telecom</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

        </a-row>
        <a-col :md="8" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <!--<a @click="handleToggleSearch" style="margin-left: 8px">-->
                <!--{{ toggleSearchStatus ? '收起' : '展开' }}-->
                <!--<a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>-->
              <!--</a>-->
              <a-button @click="handleAdd" type="primary" icon="plus" style="margin-left: 8px">新增</a-button>
            </span>
        </a-col>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">

      <!--<a-button type="primary" icon="download" @click="handleExportXls('electron_code')">导出</a-button>-->
      <!--<a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
        <!--<a-button type="primary" icon="import">导入</a-button>-->
      <!--</a-upload>-->
      <!--<a-dropdown v-if="selectedRowKeys.length > 0">-->
        <!--<a-menu slot="overlay">-->
          <!--<a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>-->
        <!--</a-menu>-->
        <!--<a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>-->
      <!--</a-dropdown>-->
    </div>

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
          <a @click="handleEdit(record)">编辑</a>
          <a-divider type="vertical" />
            <a-dropdown>

                  <a-popconfirm v-has="'electronList:deleteById'" title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                    <a>删除</a>
                  </a-popconfirm>
<!--              <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>-->

            </a-dropdown>
        </span>

      </a-table>
    </div>

    <electronCode-modal ref="modalForm" @ok="modalFormOk"></electronCode-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronCodeModal from './modules/ElectronCodeModal'

  export default {
    name: "ElectronCodeList",
    mixins:[JeecgListMixin],
    components: {
      ElectronCodeModal
    },
    data () {
      return {
        description: 'electron_code管理页面',
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
            title:'省编码',
            align:"center",
            dataIndex: 'provinceCode'
          },
          {
            title:'省',
            align:"center",
            dataIndex: 'province'
          },
          {
            title:'市编码',
            align:"center",
            dataIndex: 'cityCode'
          },
          {
            title:'市',
            align:"center",
            dataIndex: 'city'
          },
          {
            title:'区编码',
            align:"center",
            dataIndex: 'countyCode'
          },
          {
            title:'区',
            align:"center",
            dataIndex: 'county'
          },
          {
            title:'类型',
            align:"center",
            dataIndex: 'type'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/electroncode/electronCode/list",
          delete: "/electroncode/electronCode/delete",
          deleteBatch: "/electroncode/electronCode/deleteBatch",
          exportXlsUrl: "/electroncode/electronCode/exportXls",
          importExcelUrl: "electroncode/electronCode/importExcel",
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