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

        <a-form-item label="username" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'username', validatorRules.username]" placeholder="请输入username"></a-input>
        </a-form-item>
        <a-form-item label="phone" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'phone', validatorRules.phone]" placeholder="请输入phone"></a-input>
        </a-form-item>
        <a-form-item label="province" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'province', validatorRules.province]" placeholder="请输入province"></a-input>
        </a-form-item>
        <a-form-item label="city" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'city', validatorRules.city]" placeholder="请输入city"></a-input>
        </a-form-item>
        <a-form-item label="address" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'address', validatorRules.address]" placeholder="请输入address"></a-input>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: "IotCardOrderModal",
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
          username: {rules: [
          ]},
          phone: {rules: [
          ]},
          province: {rules: [
          ]},
          city: {rules: [
          ]},
          address: {rules: [
          ]},
        },
        url: {
          add: "/order/iotCardOrder/add",
          edit: "/order/iotCardOrder/edit",
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
          this.form.setFieldsValue(pick(this.model,'username','phone','province','city','address','createTime'))
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
        this.form.setFieldsValue(pick(row,'username','phone','province','city','address','createTime'))
      },

      
    }
  }
</script>