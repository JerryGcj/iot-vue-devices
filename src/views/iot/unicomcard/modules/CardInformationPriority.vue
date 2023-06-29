<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    :okButtonProps="{ props: {disabled: disableSubmit} }"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-spin :spinning="confirmLoading">
      <pie class="statistic" title="流量池统计" :dataSource="countSource" :height="350"/>
      <detail-list class="statisticlist">
        <detail-list-item term="流量池总量">{{total}}</detail-list-item>
      </detail-list>
      <detail-list class="statisticlist1">
        <detail-list-item term="流量池用量">{{usaged}}</detail-list-item>
      </detail-list>
    </a-spin>

  </a-modal>
</template>

<script>
  import { getAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import Pie from '@/components/chart/Pie'
  import DetailList from '@/components/tools/DetailList'
  const DetailListItem = DetailList.Item

  export default {
    name: "CardInformationPriority",
    components: {
      Pie,
      DetailList,
      DetailListItem
    },
    data () {
      return {
        title:"操作",
        visible: false,
        model: {},
        // 数据集
        countSource: [],
        total: '',
        usaged: '',
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },

        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
        },
        url: {
          getCountInfo: "/unicomflowpool/iotUnicomFlowPool/list"
        },
      }
    },

    methods: {
      add () {
        this.edit({});
        let url = this.url.getCountInfo;
        this.loadDate(url,{});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model))
      });

      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {

      },
      handleCancel () {
        this.close()
      },
      loadDate(url,param) {
        getAction(url,param,'get').then((res) => {
          console.log(res)
          if (res.success) {
            this.countSource = [];
            this.getCountSource(res.result);
          }else{
            var that=this;
            that.$message.warning(res.message);
          }
        })
      },
      getCountSource(data){
        console.log('data='+data.total)
        this.total = data.total+'G';
        this.usaged = data.usaged+'G';
        this.countSource.push({
          item: '流量池余量',
          count: data.total-data.usaged
        })
        this.countSource.push({
          item: '流量池用量',
          count: data.usaged
        })
      }

    }
  }
</script>

<style scoped>
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

  .statistic {
    padding: 0px !important;
    margin-top: 10px;
  }
  .statisticlist {
    margin-top: -40px;
    margin-left: 300px;
  }
  .statisticlist1 {
    margin-left: 300px;
  }
  .statistic h4 {
    margin-bottom: 20px;
    text-align: center !important;
    font-size: 24px !important;;
  }

  .statistic #canvas_1 {
    width: 100% !important;
  }
</style>