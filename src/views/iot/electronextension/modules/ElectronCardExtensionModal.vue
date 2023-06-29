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

        <a-form-item label="代理商id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusId', validatorRules.cusId]" placeholder="请输入代理商id"></a-input>
        </a-form-item>
        <a-form-item label="接入号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'accessNumber', validatorRules.accessNumber]" placeholder="请输入接入号"></a-input>
        </a-form-item>
        <a-form-item label="激活时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择激活时间" v-decorator="[ 'activeTime', validatorRules.activeTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="账户余额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'accountBalance', validatorRules.accountBalance]" placeholder="请输入账户余额" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="账户状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'accountStatus', validatorRules.accountStatus]" placeholder="请输入账户状态" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="createTime" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择createTime" v-decorator="[ 'createTime', validatorRules.createTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="updateTime" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择updateTime" v-decorator="[ 'updateTime', validatorRules.updateTime]" :trigger-change="true" style="width: 100%"/>
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
    name: "ElectronCardExtensionModal",
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
          cusId: {rules: [
          ]},
          accessNumber: {rules: [
          ]},
          activeTime: {rules: [
          ]},
          accountBalance: {rules: [
          ]},
          accountStatus: {rules: [
          ]},
          createTime: {rules: [
          ]},
          updateTime: {rules: [
          ]},
        },
        url: {
          add: "/electronextension/electronCardExtension/add",
          edit: "/electronextension/electronCardExtension/edit",
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
          this.form.setFieldsValue(pick(this.model,'cusId','accessNumber','activeTime','accountBalance','accountStatus','createTime','updateTime'))
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
        this.form.setFieldsValue(pick(row,'cusId','accessNumber','activeTime','accountBalance','accountStatus','createTime','updateTime'))
      },

      
    }
  }
</script>