<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="代理商简称">
              <a-input placeholder="代理商简称" v-model="queryParam.agentSimpleName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="代理商名称">
              <a-input placeholder="代理商名称" v-model="queryParam.agentName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="8" >
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
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
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

    <electron-channel-goal-modal ref="modalForm" @ok="modalFormOk"></electron-channel-goal-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronChannelGoalModal from './modules/ElectronChannelGoalModal'

  export default {
    name: "ElectronChannelGoalList",
    mixins:[JeecgListMixin],
    components: {
      ElectronChannelGoalModal
    },
    data () {
      return {
        description: 'electron_channel_douyin_user_relation管理页面',
        // 表头
        columns: [
          {
            title:'代理商简称',
            align:"center",
            dataIndex: 'agentSimpleName'
          },
          {
            title:'代理商名称',
            align:"center",
            dataIndex: 'agentSimpleName_dictText'
          },
          {
            title:'目标月份',
            align:"center",
            dataIndex: 'goalDate'
          },
          {
            title:'销售目标数量',
            align:"center",
            dataIndex: 'saleGoalCount'
          },
          {
            title:'销售完成数量',
            align:"center",
            dataIndex: 'saleCompleteCount'
          },
          {
            title:'备注',
            align:"center",
            dataIndex: 'remark'
          },
          {
            title:'激活目标数量',
            align:"center",
            dataIndex: 'activeGoalCount'
          },
          {
            title:'激活完成数量',
            align:"center",
            dataIndex: 'activeCompleteCount'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/electronchannelgoal/electronChannelGoal/list",
          delete: "/electronchannelgoal/electronChannelGoal/delete",
          deleteBatch: "/electronchannelgoal/electronChannelGoal/deleteBatch",
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
      handleEdit(record) {
        this.$refs.modalForm.edit(record);
        this.$refs.modalForm.title = "编辑";
        this.$refs.modalForm.disableSubmit = false;
        this.$refs.modalForm.state = false;
      },
      handleAdd: function () {
        this.$refs.modalForm.add();
        this.$refs.modalForm.title = "新增";
        this.$refs.modalForm.disableSubmit = false;
        this.$refs.modalForm.state = true;
      },

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>