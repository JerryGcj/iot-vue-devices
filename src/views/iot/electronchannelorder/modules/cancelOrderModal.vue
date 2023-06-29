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

        <a-form-item label="上游单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderNum', validatorRules.orderNum]" placeholder="请输入上游单号"></a-input>
        </a-form-item>
        <a-form-item label="取消原因类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'returnCauseType', validatorRules.returnCauseType]" placeholder="请选择取消原因类型" allowClear>
            <a-select-option value="0">客户要求取消</a-select-option>
            <a-select-option value="1">超时取消</a-select-option>
            <a-select-option value="2">客户拒签</a-select-option>
          </a-select>
        </a-form-item>
<!--        <a-form-item label="申请人" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
<!--          <a-input v-decorator="[ 'applyPerson', validatorRules.applyPerson]" placeholder="请输入下单人姓名"></a-input>-->
<!--        </a-form-item>-->
        <a-form-item label="取消原因" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'returnCause', validatorRules.returnCause]" placeholder="请输入取消原因"></a-input>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>
<script>
  import { httpAction } from '@/api/manage'

  export default {
        name: "cancelOrderModal",
      data () {
        return {
          form: this.$form.createForm(this),
          title:"操作",
          width:800,
          visible: false,
          model: {},
          dictOptions: [],
          labelCol: {
            xs: { span: 24 },
            sm: { span: 5 },
          },
          wrapperCol: {
            xs: { span: 24 },
            sm: { span: 16 },
          },
          rows:3,
          confirmLoading: false,
          validatorRules: {
            orderNum: {
              rules: [
                { required: true, message: '请输入上游单号!'}
              ]
            },
            returnCauseType: {
              rules: [
                { required: true, message: '请选择取消原因类型!'}
              ]
            },
            applyPerson: {
              rules: [
                { required: true, message: '请输入申请人!'}
              ]
            },
            returnCause: {
              rules: [
                { required: true, message: '请输入取消原因!'}
              ]
            },
          },
          url: {
            add: "/gd/mobileApi/gdCancelOrder",
          }
        }
      },
        methods:{
          add () {
            this.edit({});
          },
          edit (record) {
            this.form.resetFields();
            this.model = Object.assign({}, record);
            this.visible = true;
            this.$nextTick(() => {
            })
          },
          handleOk () {
            const that = this;
            // 触发表单验证
            this.form.validateFields((err, values) => {
              if (!err) {
                that.confirmLoading = true;
                let formData = Object.assign(this.model, values);
                console.log("表单提交数据",formData)
                httpAction(this.url.add,formData,'post').then((res)=>{
                  if(res.success){
                    that.$message.success(res.message);
                    //that.$emit('ok');
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
          close () {
            this.$emit('close');
            this.visible = false;
          },

        }
    }
</script>

<style scoped>

</style>