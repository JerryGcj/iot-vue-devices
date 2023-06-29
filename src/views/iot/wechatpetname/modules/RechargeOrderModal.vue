<template>
  <div>
    <a-modal
      centered
      :title="title"
      :width="1200"
      :visible="visible"
      :okButtonProps="{ props: {disabled: disableSubmit} }"
      @ok="handleOk"
      @cancel="handleCancel"
      cancelText="关闭">
      <!-- table区域-begin -->
      <div>
        <a-table
          bordered
          size="middle"
          rowKey="id"
          :columns="columns"
          :dataSource="dataSource"
          :pagination="ipagination"
          :loading="loading"
          @change="handleTableChange">

          <span slot="transactionId" slot-scope="text">
            <j-ellipsis :value="text" :length="18" />
          </span>
          <span slot="id" slot-scope="text">
            <j-ellipsis :value="text" :length="18" />
          </span>
        </a-table>
      </div>
      <!-- table区域-end -->


    </a-modal>
  </div>
</template>

<script>
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import JEllipsis from "@/components/jeecg/JEllipsis"

  export default {
    name: "RechargeOrderModal",
    mixins:[JeecgListMixin],
    components: {
      JEllipsis
    },
    data() {
      return {
        title: "",
        visible: false,
        placement: 'right',
        description: '',
        // 查询条件
        queryParam: {
          openId:""
        },
        confirmLoading: false,
        // 表头
        columns: [
          {
            title:'预充订单号',
            align:"center",
            dataIndex: 'id',
            scopedSlots: {customRender: 'id'}
          },
          {
            title:'充值用户手机号',
            align:"center",
            dataIndex: 'mobile'
          },
          {
            title:'企业名称',
            align:"center",
            dataIndex: 'storeId_dictText'
          },
          {
            title:'公众号',
            align:"center",
            dataIndex: 'appid_dictText'
          },
          {
            title:'微信支付单号',
            align:"center",
            dataIndex: 'transactionId',
            scopedSlots: {customRender: 'transactionId'}
          },
          {
            title:'充值产品',
            align:"center",
            dataIndex: 'productId_dictText'
          },
          {
            title:'充值金额(元)',
            align:"center",
            dataIndex: 'money'
          },
          {
            title:'状态',
            align:"center",
            dataIndex: 'status_dictText'
          },
          {
            title:'充值时间',
            align:"center",
            dataIndex: 'createTime'
          }
        ],
        // 分页参数
        ipagination: {
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        isorter: {
          column: 'createTime',
          order: 'desc',
        },
        loading: false,
        url: {
          list: "/order/iotRechargeOrder/list"
        }
      }
    },
    methods: {
      edit(record) {
        console.log(JSON.stringify(record))
        this.visible = true;
        this.queryParam.openId = record;
        // 当其它模块调用该模块时,调用此方法加载字典数据
        this.loadData();
      },
      handleCancel() {
        this.visible = false;
      },
      handleOk() {
        this.dataSource2 = this.selectedRowKeys;
        console.log("data:" + this.dataSource2);
        this.$emit("selectFinished", this.dataSource2);
        this.visible = false;
      },
      handleTableChange(pagination, filters, sorter) {
        //分页、排序、筛选变化时触发
        console.log(sorter);
        //TODO 筛选
        if (Object.keys(sorter).length > 0) {
          this.isorter.column = sorter.field;
          this.isorter.order = "ascend" == sorter.order ? "asc" : "desc"
        }
        this.ipagination = pagination;
        this.loadData();
      }
    }
  }
</script>
<style lang="less" scoped>
  .ant-card-body .table-operator {
    margin-bottom: 18px;
  }

  .ant-table-tbody .ant-table-row td {
    padding-top: 15px;
    padding-bottom: 15px;
  }

  .anty-row-operator button {
    margin: 0 5px
  }

  .ant-btn-danger {
    background-color: #ffffff
  }

  .ant-modal-cust-warp {
    height: 100%
  }

  .ant-modal-cust-warp .ant-modal-body {
    height: calc(100% - 110px) !important;
    overflow-y: auto
  }

  .ant-modal-cust-warp .ant-modal-content {
    height: 90% !important;
    overflow-y: hidden
  }
</style>