<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="用户账号">
              <a-input placeholder="请输入用户账号" v-model="queryParam.userId" allow-clear></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="联系人">
              <a-input placeholder="请输入联系人" v-model="queryParam.realname" allow-clear></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="8">
            <a-form-item label="产品名称">
              <a-input placeholder="请输入产品名称" v-model="queryParam.agentName" allow-clear></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="8">
            <a-form-item label="每日限额">
              <a-input placeholder="请输入每日限额" v-model="queryParam.limitCount_begin" allow-clear></a-input>
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

        <span slot="action" slot-scope="text, record">
            <a @click="handleEdit(record)">编辑</a>
        </span>

      </a-table>
    </div>
    <EditLimitCountModal ref="editLimitCountModal"  @ok="modalFormOk"/>

  </a-card>
</template>

<script>
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import EditLimitCountModal from './modules/EditLimitCountModal'

  export default {
        name: "ElectronChannelUserList",
        mixins:[JeecgListMixin],
        components: {
          EditLimitCountModal
        },
        data() {
          return {
            isorter:{
              column:'',
              order:''
            },
            columns: [
              {
                title:'用户账号',
                align:"center",
                dataIndex: 'userId_dictText',
              },
              {
                title:'联系人',
                align:"center",
                dataIndex: 'realname',
              },
              {
                title:'产品名称',
                align:"center",
                dataIndex: 'agentId_dictText',
              },
              {
                title:'每日限额',
                align:"center",
                dataIndex: 'limitCount',
              },
              {
                title: '操作',
                dataIndex: 'action',
                align:"center",
                scopedSlots: { customRender: 'action' },
              }
            ],
            url: {
              list:"/electronchanneluser/electronChannelUser/list"
            }
          }
        },
      methods: {
        handleEdit(data) {
          this.$refs.editLimitCountModal.edit(data)
          this.$refs.editLimitCountModal.title = "编辑";
          this.$refs.editLimitCountModal.disableSubmit = false;
        }
      }
    }
</script>

<style scoped>

</style>