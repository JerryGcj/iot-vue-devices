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

        <a-form-item label="提现客户" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-tree-select
            v-decorator="[ 'userId', validatorRules.userId]"
            placeholder="请选择客户"
            dict="sys_user,user_company,id"
            condition='{"del_flag":"0"}'
            pidField="create_by_id"
            pidValue=""/>
        </a-form-item>
        <a-form-item label="收款银行卡号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'bankAccount', validatorRules.bankAccount]" placeholder="请输入收款银行卡号"></a-input>
        </a-form-item>
        <a-form-item label="提现金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'money', validatorRules.money]" placeholder="请输入提现金额(元)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="提现类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select :dropdownStyle="{ maxHeight: '200px', overflow: 'auto' }"
                    v-decorator="[ 'withdrawalType', validatorRules.withdrawalType]"
                    placeholder="请选择提现类型">
            <a-select-option value="0">预付款</a-select-option>
          </a-select>
          <!--<j-dict-select-tag type="list" v-decorator="['withdrawalType', validatorRules.withdrawalType]" :trigger-change="true" dictCode="预付款_0" placeholder="请选择提现类型"/>-->
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'applyRemark', validatorRules.applyRemark]"></a-input>
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
  import JDate from '@/components/jeecg/JDate'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  import JTreeSelect from '@/components/jeecg/JTreeSelect'

  export default {
    name: "IotWithdrawDepositModal",
    components: {
      JDate,
      JDictSelectTag,
      JTreeSelect
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
          realName: {rules: [
              {required: true, message: '请选择客户!'},
            ]},
          bankAccount: {rules: [
              {required: true, message: '请输入银行卡号!'},
            ]},
          money: {rules: [
              {required: true, message: '请输入提现金额!'},
            ]},
          withdrawalType: {rules: [
              {required: true, message: '请选择提现类型!'},
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
          this.form.setFieldsValue(pick(this.model,'orderNo','userId','userName','userCompany','applyRemark','bankAccount','money','withdrawalType','withdrawalWay','auditUser','paymentUser','proofTrading','auditRemark','auditStatus','createTime','createUser','updateTime','updateUser'))
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
        this.form.setFieldsValue(pick(row,'orderNo','userId','userName','userCompany','applyRemark','bankAccount','money','withdrawalType','withdrawalWay','auditUser','paymentUser','proofTrading','auditRemark','auditStatus','createTime','createUser','updateTime','updateUser'))
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