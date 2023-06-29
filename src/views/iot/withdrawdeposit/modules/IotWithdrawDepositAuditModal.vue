<template>
  <a-drawer
    :title="title"
    :width="width"
    placement="right"
    :closable="false"
    @close="close"
    :visible="visible">
  
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <!--<a-form-item label="提现单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input disabled="" v-decorator="[ 'orderNo', validatorRules.orderNo]"></a-input>
        </a-form-item>-->
        <a-form-item label="提现客户" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input disabled="" v-decorator="[ 'userCompany', validatorRules.userCompany]"></a-input>
        </a-form-item>

        <a-form-item label="账号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input disabled="" v-decorator="[ 'bankAccount', validatorRules.bankAccount]"></a-input>
        </a-form-item>
        <a-form-item label="提现金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number disabled="" v-decorator="[ 'money', validatorRules.money]" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="提现方式" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select disabled="" v-decorator="[ 'withdrawalWay', validatorRules.withdrawalWay]">
            <a-select-option value="0">银行</a-select-option>
            <a-select-option value="1">微信</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input disabled="" v-decorator="[ 'applyRemark', validatorRules.applyRemark]"></a-input>
        </a-form-item>
        <a-form-item label="审核备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'auditRemark', validatorRules.auditRemark]" placeholder="请输入审核备注"></a-input>
        </a-form-item>
        <a-form-item label="审核状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'auditStatus',  {  initialValue: '1' }]" >
            <a-select-option value="1">审核通过</a-select-option>
            <a-select-option value="2">审核不通过</a-select-option>
          </a-select>
        </a-form-item>
      </a-form>
    </a-spin>
    <a-button type="primary" @click="handleOk">提交</a-button>
    <a-button type="primary" @click="handleCancel">取消</a-button>
  </a-drawer>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JDate from '@/components/jeecg/JDate'  
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  
  export default {
    name: "IotWithdrawDepositModal",
    components: { 
      JDate,
      JDictSelectTag,
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
          orderNo: {rules: [
          ]},
          userId: {rules: [
          ]},
          userName: {rules: [
          ]},
          userCompany: {rules: [
          ]},
          applyRemark: {rules: [
          ]},
          bankAccount: {rules: [
          ]},
          money: {rules: [
          ]},
          withdrawalType: {rules: [
          ]},
          withdrawalWay: {rules: [
          ]},
          auditUser: {rules: [
          ]},
          paymentUser: {rules: [
          ]},
          proofTrading: {rules: [
          ]},
          auditRemark: {rules: [
          ]},
          auditStatus: {rules: [
          ]},
          createTime: {rules: [
          ]},
          createUser: {rules: [
          ]},
          updateTime: {rules: [
          ]},
          updateUser: {rules: [
          ]},
        },
        url: {
          add: "/withdrawdeposit/iotWithdrawDeposit/add",
          edit: "/withdrawdeposit/iotWithdrawDeposit/edit",
        }
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'orderNo','userId','userName','userCompany','applyRemark','bankAccount','money','withdrawalType','withdrawalWay','auditUser','paymentUser','proofTrading','auditRemark','createTime','createUser','updateTime','updateUser'))
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            let formData = Object.assign(this.model, values);
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }
         
        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'orderNo','userId','userName','userCompany','applyRemark','bankAccount','money','withdrawalType','withdrawalWay','auditUser','paymentUser','proofTrading','auditRemark','createTime','createUser','updateTime','updateUser'))
      }
      
    }
  }
</script>

<style lang="less" scoped>
/** Button按钮间距 */
  .ant-btn {
    margin-left: 30px;
    margin-bottom: 30px;
    float: right;
  }
</style>