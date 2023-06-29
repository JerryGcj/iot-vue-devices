<template>
  <a-modal
    title="产品删除"
    :width="600"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleCancel"
    @cancel="handleCancel"
    cancelText="关闭"
    style="top:20px;"
  >
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
        <a-menu-item>
          <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
            <a v-has="'user:delete'">删除</a>
          </a-popconfirm>
        </a-menu-item>
      </span>
    </a-table>
  </a-modal>
</template>

<script>
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
    export default {
        name: "DelChannelModal",
        mixins:[JeecgListMixin],
        data () {
          return {
            visible: false,
            queryParam: {
              userId: "",
            },
            columns: [
              {
                title:'通道名称',
                align:"center",
                dataIndex: 'agentName',
                width: 110
              },
              {
                title: '操作',
                dataIndex: 'action',
                scopedSlots: {customRender: 'action'},
                align: "center",
                width: 50
              },
            ],
            url: {
              list: "/electronchanneluser/electronChannelUser/list",
              delete: "/electronchanneluser/electronChannelUser/delete",
            }
          }
        },
        methods: {
          edit (record) {
            this.queryParam.userId = record.id
            this.visible = true;
            this.loadData(1);
          },
          handleCancel () {
            this.queryParam.userId = ""
            this.visible = false
          },
        }
    }
</script>

<style scoped>

</style>