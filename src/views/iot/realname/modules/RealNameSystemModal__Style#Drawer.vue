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

        <a-form-item label="集成电路卡识别码20位字符" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'iccid', validatorRules.iccid]" placeholder="请输入集成电路卡识别码20位字符"></a-input>
        </a-form-item>
        <a-form-item label="物联卡号码最长13位数字" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'msisdn', validatorRules.msisdn]" placeholder="请输入物联卡号码最长13位数字"></a-input>
        </a-form-item>
        <a-form-item label="身份证号码
身份证号码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'idCardNumber', validatorRules.idCardNumber]" placeholder="请输入身份证号码
身份证号码"></a-input>
        </a-form-item>
        <a-form-item label="手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'mobile', validatorRules.mobile]" placeholder="请输入手机号"></a-input>
        </a-form-item>
        <a-form-item label="商户名" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['userCompany', validatorRules.userCompany]" :trigger-change="true" dictCode="" placeholder="请选择商户名"/>
        </a-form-item>
        <a-form-item label="请求流水号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'serialNumber', validatorRules.serialNumber]" placeholder="请输入请求流水号"></a-input>
        </a-form-item>
        <a-form-item label="状态（0：待审核，1：成功，2：失败）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['status', validatorRules.status]" :trigger-change="true" dictCode="" placeholder="请选择状态（0：待审核，1：成功，2：失败）"/>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'remark', validatorRules.remark]" placeholder="请输入备注"></a-input>
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
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  
  export default {
    name: "RealNameSystemModal",
    components: { 
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
          iccid: {rules: [
          ]},
          msisdn: {rules: [
          ]},
          idCardNumber: {rules: [
          ]},
          mobile: {rules: [
          ]},
          userCompany: {rules: [
          ]},
          serialNumber: {rules: [
          ]},
          status: {rules: [
          ]},
          remark: {rules: [
          ]},
        },
        url: {
          add: "/realname/realNameSystem/add",
          edit: "/realname/realNameSystem/edit",
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
          this.form.setFieldsValue(pick(this.model,'iccid','msisdn','idCardNumber','mobile','userCompany','serialNumber','status','remark'))
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
        this.form.setFieldsValue(pick(row,'iccid','msisdn','idCardNumber','mobile','userCompany','serialNumber','status','remark'))
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