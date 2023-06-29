<template>
  <a-modal
    :title="title"
    :width="width"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="运营商id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'operatorId', validatorRules.operatorId]" placeholder="请输入运营商id"></a-input>
        </a-form-item>
        <a-form-item label="接入号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'accessNumber', validatorRules.accessNumber]" placeholder="请输入接入号"></a-input>
        </a-form-item>
        <a-form-item label="基础套餐价格" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'standardPrice', validatorRules.standardPrice]" placeholder="请输入基础套餐价格" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="首充奖励折扣比" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'firstRewardDiscount', validatorRules.firstRewardDiscount]" placeholder="请输入首充奖励折扣比" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="分佣比" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'commissionRatio', validatorRules.commissionRatio]" placeholder="请输入分佣比" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="佣金" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'commission', validatorRules.commission]" placeholder="请输入佣金" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="入网月份" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'activateDate', validatorRules.activateDate]" placeholder="请输入入网月份"></a-input>
        </a-form-item>
        <a-form-item label="佣金月份" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'commissionDate', validatorRules.commissionDate]" placeholder="请输入佣金月份"></a-input>
        </a-form-item>
        <a-form-item label="createTime" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择createTime" v-decorator="[ 'createTime', validatorRules.createTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="createBy" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'createBy', validatorRules.createBy]" placeholder="请输入createBy"></a-input>
        </a-form-item>
        <a-form-item label="updateTime" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择updateTime" v-decorator="[ 'updateTime', validatorRules.updateTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="updateBy" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'updateBy', validatorRules.updateBy]" placeholder="请输入updateBy"></a-input>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JDate from '@/components/jeecg/JDate'  

  export default {
    name: "ElectronOperationIncomeModal",
    components: { 
      JDate,
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
          operatorId: {rules: [
          ]},
          accessNumber: {rules: [
          ]},
          standardPrice: {rules: [
          ]},
          firstRewardDiscount: {rules: [
          ]},
          commissionRatio: {rules: [
          ]},
          commission: {rules: [
          ]},
          activateDate: {rules: [
          ]},
          commissionDate: {rules: [
          ]},
          createTime: {rules: [
          ]},
          createBy: {rules: [
          ]},
          updateTime: {rules: [
          ]},
          updateBy: {rules: [
          ]},
        },
        url: {
          add: "/electronoperationincome/electronOperationIncome/add",
          edit: "/electronoperationincome/electronOperationIncome/edit",
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
          this.form.setFieldsValue(pick(this.model,'operatorId','accessNumber','standardPrice','firstRewardDiscount','commissionRatio','commission','activateDate','commissionDate','createTime','createBy','updateTime','updateBy'))
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
        this.form.setFieldsValue(pick(row,'operatorId','accessNumber','standardPrice','firstRewardDiscount','commissionRatio','commission','activateDate','commissionDate','createTime','createBy','updateTime','updateBy'))
      },

      
    }
  }
</script>