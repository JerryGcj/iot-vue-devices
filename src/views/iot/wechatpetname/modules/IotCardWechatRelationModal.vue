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

        <a-form-item label="卡号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'iccid', validatorRules.iccid]" placeholder="请输入卡号"></a-input>
        </a-form-item>
        <a-form-item label="openid" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'openId', validatorRules.openId]" placeholder="请输入openid"></a-input>
        </a-form-item>
        <a-form-item label="昵称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'petName', validatorRules.petName]" placeholder="请输入昵称"></a-input>
        </a-form-item>
        <a-form-item label="createDate" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择createDate" v-decorator="[ 'createDate', validatorRules.createDate]" :trigger-change="true" style="width: 100%"/>
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
    name: "IotCardWechatRelationModal",
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
          iccid: {rules: [
          ]},
          openId: {rules: [
          ]},
          petName: {rules: [
          ]},
          createDate: {rules: [
          ]},
        },
        url: {
          add: "/wechatpetname/iotCardWechatRelation/add",
          edit: "/wechatpetname/iotCardWechatRelation/edit",
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
          this.form.setFieldsValue(pick(this.model,'iccid','openId','petName','createDate'))
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
        this.form.setFieldsValue(pick(row,'iccid','openId','petName','createDate'))
      },

      
    }
  }
</script>