<template>
  <div>
    <a-modal
      centered
      :title="title"
      :width="1400"
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

          <template slot="PayState" slot-scope="state">
            <a-tag v-if="state==0" color="gray">未支付</a-tag>
            <a-tag v-if="state==1" color="cyan">支付退出</a-tag>
            <a-tag v-if="state==2" color="purple">支付异常</a-tag>
            <a-tag v-if="state==3" color="red">支付失败</a-tag>
            <a-tag v-if="state==4" color="green">支付成功</a-tag>
            <a-tag v-if="state==5" color="orange">退款失败</a-tag>
            <a-tag v-if="state==6" color="orange">退款成功</a-tag>
          </template>
        </a-table>
      </div>
      <!-- table区域-end -->


    </a-modal>
  </div>
</template>

<script>
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import {filterObj} from '@/utils/util'

  export default {
    name: "OrderModal",
    mixins:[JeecgListMixin],
    data() {
      return {
        title: "",
        visible: false,
        placement: 'right',
        description: '',
        // 查询条件
        queryParam: {
          iccid:""
        },
        confirmLoading: false,
        // 表头
        columns: [
          {
            title: 'ICCID',
            align:"center",
            dataIndex: 'iccid',
            width: 200
          },
          {
            title: '支付状态',
            align:"center",
            dataIndex: 'payState',
            width: 100,
            scopedSlots: { customRender: 'PayState' }
          },
          {
            title: '订单状态',
            align:"center",
            dataIndex: 'orderState_dictText',
            width: 100

          },
          {
            title: '运营商',
            align:"center",
            dataIndex: 'operatorType',
            width: 100,
            customRender:function (text) {
              if(text=='1'){
                return "移动";
              }else if(text=="2"){
                return "联通";
              }else if(text=="3"){
                return "电信";
              } else {
                return text;
              }
            }
          },
          {
            title: '套餐名称',
            align:"center",
            dataIndex: 'packageName',
          },
          {
            title: '交易金额（元）',
            align:"center",
            dataIndex: 'tradingMoney',
            width: 120
          },
          {
            title: '购买数量',
            align:"center",
            dataIndex: 'buyNumber',
            width: 80
          },
          {
            title: '公司名称',
            align:"center",
            dataIndex: 'companyName',
            width: 150,
          },
          {
            title: '订单创建时间',
            align:"center",
            dataIndex: 'createTime',
            width: 180
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
          list: "/order/order/list"
        }
      }
    },
    methods: {
      edit(record) {
        console.log(JSON.stringify(record))
        this.visible = true;
        this.queryParam.iccid = record;
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