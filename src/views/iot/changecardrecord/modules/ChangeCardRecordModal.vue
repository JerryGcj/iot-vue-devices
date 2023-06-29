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

        <a-form-item label="转移后余额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'afterBalance', validatorRules.afterBalance]" placeholder="请输入转移后余额" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="转移前余额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'beforeBalance', validatorRules.beforeBalance]" placeholder="请输入转移前余额" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="操作人" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'createBy', validatorRules.createBy]" placeholder="请输入操作人"></a-input>
        </a-form-item>
        <a-form-item label="操作时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择操作时间" v-decorator="[ 'createTime', validatorRules.createTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="新卡号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'newIccid', validatorRules.newIccid]" placeholder="请输入新卡号"></a-input>
        </a-form-item>
        <a-form-item label="新手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'newMobile', validatorRules.newMobile]" placeholder="请输入新手机号"></a-input>
        </a-form-item>
        <a-form-item label="旧卡号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'oldIccid', validatorRules.oldIccid]" placeholder="请输入旧卡号"></a-input>
        </a-form-item>
        <a-form-item label="旧手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'oldMobile', validatorRules.oldMobile]" placeholder="请输入旧手机号"></a-input>
        </a-form-item>
        <a-form-item label="openid" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'openid', validatorRules.openid]" placeholder="请输入openid"></a-input>
        </a-form-item>
        <a-form-item label="操作类型 0：换卡；1：转移余额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'operationType', validatorRules.operationType]" placeholder="请输入操作类型 0：换卡；1：转移余额"></a-input>
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
    name: "ChangeCardRecordModal",
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
          afterBalance: {rules: [
          ]},
          beforeBalance: {rules: [
          ]},
          createBy: {rules: [
          ]},
          createTime: {rules: [
          ]},
          newIccid: {rules: [
          ]},
          newMobile: {rules: [
          ]},
          oldIccid: {rules: [
          ]},
          oldMobile: {rules: [
          ]},
          openid: {rules: [
          ]},
          operationType: {rules: [
          ]},
        },
        url: {
          add: "/changecardrecord/changeCardRecord/add",
          edit: "/changecardrecord/changeCardRecord/edit",
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
          this.form.setFieldsValue(pick(this.model,'afterBalance','beforeBalance','createBy','createTime','newIccid','newMobile','oldIccid','oldMobile','openid','operationType'))
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
        this.form.setFieldsValue(pick(row,'afterBalance','beforeBalance','createBy','createTime','newIccid','newMobile','oldIccid','oldMobile','openid','operationType'))
      },

      
    }
  }
</script>