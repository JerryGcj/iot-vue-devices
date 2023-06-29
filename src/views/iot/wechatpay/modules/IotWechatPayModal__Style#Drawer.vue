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

        <a-form-item label="商户名" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'mchName', validatorRules.mchName]" placeholder="请输入商户名"></a-input>
        </a-form-item>
        <a-form-item label="开发者ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appId', validatorRules.appId]" placeholder="请输入开发者ID"></a-input>
        </a-form-item>
        <a-form-item label="开发者秘钥" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appSecret', validatorRules.appSecret]" placeholder="请输入开发者秘钥"></a-input>
        </a-form-item>
        <a-form-item label="商户id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'mchId', validatorRules.mchId]" placeholder="请输入商户id"></a-input>
        </a-form-item>
        <a-form-item label="商户秘钥" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'mchKey', validatorRules.mchKey]" placeholder="请输入商户秘钥"></a-input>
        </a-form-item>
        <a-form-item label="域名" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'domainName', validatorRules.domainName]" placeholder="请输入域名"></a-input>
        </a-form-item>
        
      </a-form>
    </a-spin>
    <a-button type="primary" @click="handleOk">确定</a-button>
    <a-button type="primary" @click="handleCancel">取消</a-button>
  </a-drawer>
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
          ]},
          appId: {rules: [
          ]},
          appSecret: {rules: [
          ]},
          mchId: {rules: [
          ]},
          mchKey: {rules: [
          ]},
          domainName: {rules: [
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
      add () {
        this.edit({});
      },
      edit (record) {
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