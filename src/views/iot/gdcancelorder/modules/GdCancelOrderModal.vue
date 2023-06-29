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

        <a-form-item label="订单regular表id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderId', validatorRules.orderId]" placeholder="请输入订单regular表id"></a-input>
        </a-form-item>
        <a-form-item label="渠道编号： EB002" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'chnlCode', validatorRules.chnlCode]" placeholder="请输入渠道编号： EB002"></a-input>
        </a-form-item>
        <a-form-item label="上游返回单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderNum', validatorRules.orderNum]" placeholder="请输入上游返回单号"></a-input>
        </a-form-item>
        <a-form-item label="取消原因类型： 0 客户要求取消,1 超时取消;2 客户拒签" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'returnCauseType', validatorRules.returnCauseType]" placeholder="请输入取消原因类型： 0 客户要求取消,1 超时取消;2 客户拒签"></a-input>
        </a-form-item>
        <a-form-item label="申请人" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'applyPerson', validatorRules.applyPerson]" placeholder="请输入申请人"></a-input>
        </a-form-item>
        <a-form-item label="取消原因" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'returnCause', validatorRules.returnCause]" placeholder="请输入取消原因"></a-input>
        </a-form-item>
        <a-form-item label="取消操作员" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'operId', validatorRules.operId]" placeholder="请输入取消操作员"></a-input>
        </a-form-item>
        <a-form-item label="取消渠道： APP_CODE" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'returnChnl', validatorRules.returnChnl]" placeholder="请输入取消渠道： APP_CODE"></a-input>
        </a-form-item>
        <a-form-item label="退款金额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'refundMoney', validatorRules.refundMoney]" placeholder="请输入退款金额"></a-input>
        </a-form-item>
        <a-form-item label="申请联系人" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'linkMothod', validatorRules.linkMothod]" placeholder="请输入申请联系人"></a-input>
        </a-form-item>
        <a-form-item label="创建者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'createBy', validatorRules.createBy]" placeholder="请输入创建者"></a-input>
        </a-form-item>
        <a-form-item label="创建时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择创建时间" v-decorator="[ 'createTime', validatorRules.createTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="更新者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'updateBy', validatorRules.updateBy]" placeholder="请输入更新者"></a-input>
        </a-form-item>
        <a-form-item label="更新时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择更新时间" v-decorator="[ 'updateTime', validatorRules.updateTime]" :trigger-change="true" style="width: 100%"/>
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
    name: "GdCancelOrderModal",
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
          orderId: {rules: [
          ]},
          chnlCode: {rules: [
          ]},
          orderNum: {rules: [
          ]},
          returnCauseType: {rules: [
          ]},
          applyPerson: {rules: [
          ]},
          returnCause: {rules: [
          ]},
          operId: {rules: [
          ]},
          returnChnl: {rules: [
          ]},
          refundMoney: {rules: [
          ]},
          linkMothod: {rules: [
          ]},
          createBy: {rules: [
          ]},
          createTime: {rules: [
          ]},
          updateBy: {rules: [
          ]},
          updateTime: {rules: [
          ]},
        },
        url: {
          add: "/gdcancelorder/gdCancelOrder/add",
          edit: "/gdcancelorder/gdCancelOrder/edit",
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
          this.form.setFieldsValue(pick(this.model,'orderId','chnlCode','orderNum','returnCauseType','applyPerson','returnCause','operId','returnChnl','refundMoney','linkMothod','createBy','createTime','updateBy','updateTime'))
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
        this.form.setFieldsValue(pick(row,'orderId','chnlCode','orderNum','returnCauseType','applyPerson','returnCause','operId','returnChnl','refundMoney','linkMothod','createBy','createTime','updateBy','updateTime'))
      },

      
    }
  }
</script>