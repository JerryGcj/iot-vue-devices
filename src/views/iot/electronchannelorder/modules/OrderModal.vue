<template>
  <a-modal
    :title="title"
    :width="500"
    :visible="visible"
    :okButtonProps="{ props: {disabled: disableSubmit} }"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-card :bordered="false">
      <pre>{{apiInfo}}</pre>
    </a-card>

  </a-modal>
</template>

<script>
    export default {
        name: "OrderModal",
        data () {
          return {
            title:"",
            visible: false,
            labelCol: {
              xs: { span: 24 },
              sm: { span: 5 },
            },
            wrapperCol: {
              xs: { span: 24 },
              sm: { span: 16 },
            },
            apiInfo:{},
          }
        },
      methods: {
        add (record) {
          this.apiInfo.单号 = record.id;
          this.apiInfo.外部订单号 = record.outTradeNo;
          this.apiInfo.上游单号 = record.orderNum;
          this.apiInfo.创建人 = 'qianxun';
          this.apiInfo.用户账号 = record.userName;
          this.apiInfo.代理商 = record.cusId;
          this.apiInfo.客户姓名 = record.cusName;
          this.apiInfo.客户手机号 = record.cusPhone;
          this.apiInfo.省 = record.province;
          this.apiInfo.市 = record.city;
          this.apiInfo.区 = record.district;
          this.apiInfo.详细地址 = record.detailAddr;
          this.apiInfo.订单状态 = record.orderStatus;
          this.apiInfo.身份证号 = record.cusIdno;
          this.apiInfo.快递单号 = record.expressNo;
          this.apiInfo.快递公司 = record.expressCompany;
          this.apiInfo.接入号 = record.accessNumber;
          this.apiInfo.渠道名称 = record.channelName;
          this.apiInfo.收单日期 = record.createTime;
          this.apiInfo.提单日期 = record.commitTime;
          this.apiInfo.激活日期 = record.activationDate;



















          this.visible = true;
        },
        close () {
          this.$emit('close');
          this.visible = false;
        },
        handleCancel () {
          this.close()
        },
      }
    }
</script>

<style scoped>

</style>