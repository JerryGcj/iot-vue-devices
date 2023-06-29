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

        <a-form-item label="公众号名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'mchName', validatorRules.mchName]" placeholder="请输入商户名"></a-input>
        </a-form-item>
        <a-form-item label="APPID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appId', validatorRules.appId]" placeholder="请输入开发者ID" :readonly="this.readonly" :disabled="this.readonly"></a-input>
        </a-form-item>
        <a-form-item label="开发者秘钥" :labelCol="labelCol" :wrapperCol="wrapperCol" >
          <a-input v-decorator="[ 'appSecret', validatorRules.appSecret]" placeholder="请输入开发者秘钥" :readonly="this.readonly" :disabled="this.readonly"></a-input>
        </a-form-item>
        <a-form-item label="商户号" :labelCol="labelCol" :wrapperCol="wrapperCol" >
          <a-input v-decorator="[ 'mchId', validatorRules.mchId]" placeholder="请输入商户id" :readonly="this.readonly" :disabled="this.readonly"></a-input>
        </a-form-item>
<!--        <a-form-item label="商户秘钥" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
<!--          <a-input v-decorator="[ 'mchKey', validatorRules.mchKey]" placeholder="请输入商户秘钥"></a-input>-->
<!--        </a-form-item>-->
<!--        <a-form-item label="域名" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
<!--          <a-input v-decorator="[ 'domainName', validatorRules.domainName]" placeholder="请输入域名"></a-input>-->
<!--        </a-form-item>-->

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: "IotWechatPayModal",
    components: { 
    },
    data () {
      return {
        readonly:false,
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
          mchName: {rules: [
              { required: true, message: '请输入商户名!' }
          ]},
          appId: {rules: [
              { required: true, message: '请输入开发者ID!' }
          ]},
          appSecret: {rules: [
              { required: true, message: '请输入开发者秘钥!' }
          ]},
          mchId: {rules: [
              { required: true, message: '请输入商户id!' }
          ]},
        },
        url: {
          add: "/wechatpay/iotWechatPay/add",
          edit: "/wechatpay/iotWechatPay/edit",
        }
      }
    },
    created () {
    },
    methods: {
      add (tag) {
        debugger
        if (!tag) {
          this.edit({},tag);
        } else {
          this.edit({},true);
        }

      },
      edit (record,tag) {
        this.readonly = tag
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'mchName','appId','appSecret','mchId','mchKey','domainName'))
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
        // this.readonly = !this.readonly
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
        this.form.setFieldsValue(pick(row,'mchName','appId','appSecret','mchId','mchKey','domainName'))
      },

      
    }
  }
</script>