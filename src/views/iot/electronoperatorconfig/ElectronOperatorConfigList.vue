<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="16">
            <a-form-item label="运营商">
              <a-input placeholder="请输入运营商" v-model="queryParam.operator" allow-clear></a-input>
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
<!--    <div class="table-operator">-->
<!--      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>-->
<!--    </div>-->

    <!-- table区域-begin -->
    <div>
<!--      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">-->
<!--        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项-->
<!--        <a style="margin-left: 24px" @click="onClearSelected">清空</a>-->
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

    <electronOperatorConfig-modal ref="modalForm" @ok="modalFormOk"></electronOperatorConfig-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronOperatorConfigModal from './modules/ElectronOperatorConfigModal'

  export default {
    name: "ElectronOperatorConfigList",
    mixins:[JeecgListMixin],
    components: {
      ElectronOperatorConfigModal
    },
    data () {
      return {
        description: 'electron_operator_config管理页面',
        // 表头
        columns: [
          // {
          //   title: '#',
          //   dataIndex: '',
          //   key:'rowIndex',
          //   width:60,
          //   align:"center",
          //   customRender:function (t,r,index) {
          //     return parseInt(index)+1;
          //   }
          // },
          {
            title:'运营商',
            align:"center",
            dataIndex: 'operator'
          },
          {
            title:'预估在网时长',
            align:"center",
            dataIndex: 'estimatedOnlineDuration'
          },
          {
            title:'创建时间',
            align:"center",
            dataIndex: 'createTime',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
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
          list: "/electronoperatorconfig/electronOperatorConfig/list",
          delete: "/electronoperatorconfig/electronOperatorConfig/delete",
          deleteBatch: "/electronoperatorconfig/electronOperatorConfig/deleteBatch",
          exportXlsUrl: "/electronoperatorconfig/electronOperatorConfig/exportXls",
          importExcelUrl: "electronoperatorconfig/electronOperatorConfig/importExcel",
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
      },
      handleEdit: function (record) {
        this.$refs.modalForm.edit(record);
        this.$refs.modalForm.tag = false;
        this.$refs.modalForm.title = "编辑";
        this.$refs.modalForm.disableSubmit = false;
      },
      handleAdd: function () {
        this.$refs.modalForm.add();
        this.$refs.modalForm.title = "新增";
        this.$refs.modalForm.tag = true;
        this.$refs.modalForm.disableSubmit = false;
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>