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

        <a-form-item label="使用状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag placeholder="请选择使用状态" :trigger-change="true" v-decorator="[ 'enableStatus', validatorRules.enableStatus]"  dict-code="enable_status"></j-dict-select-tag>
        </a-form-item>
        <a-form-item label="标签名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'tagName', validatorRules.tagName]" placeholder="请输入标签名称"></a-input>
        </a-form-item>
        <a-form-item label="标签描述" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea v-decorator="['tagDesc', validatorRules.tagDesc]" rows="4" placeholder="请输入标签描述"/>
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
    name: "IotConsumerServiceTagModal",
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
          tagName: {rules: [
              {required: true, message: '请选择解决状态'},
            ]},
          enableStatus: {rules: [
              {required: true, message: '请选择解决状态'},
            ]},
        },
        url: {
          add: "/consumertag/iotConsumerServiceTag/add",
          edit: "/consumertag/iotConsumerServiceTag/edit",
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
          this.form.setFieldsValue(pick(this.model,'tagName','tagDesc','enableStatus','createTime','createBy'))
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
        this.form.setFieldsValue(pick(row,'tagName','tagDesc','enableStatus','createTime','createBy'))
      },

      
    }
  }
</script>