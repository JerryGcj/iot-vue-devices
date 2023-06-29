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

        <a-form-item label="产品简称缩写" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'productSimpleName', validatorRules.productSimpleName]" placeholder="请输入产品简称缩写"></a-input>
        </a-form-item>
        <a-form-item label="产品名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'productName', validatorRules.productName]" placeholder="请输入产品名称"></a-input>
        </a-form-item>
        <a-form-item label="代理商id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'agentId', validatorRules.agentId]" placeholder="请输入代理商id"></a-input>
        </a-form-item>
        <a-form-item label="销售品名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'packageName', validatorRules.packageName]" placeholder="请输入套餐名称"></a-input>
        </a-form-item>
        <a-form-item label="套餐代码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'packageCode', validatorRules.packageCode]" placeholder="请输入套餐代码"></a-input>
        </a-form-item>
        <a-form-item label="发展人工号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'devStaffNum', validatorRules.devStaffNum]" placeholder="请输入发展人工号"></a-input>
        </a-form-item>

        <a-form-item label="私钥" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'privateKey', validatorRules.privateKey]" placeholder="请输入私钥"></a-input>
        </a-form-item>
        <a-form-item label="appId" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appId', validatorRules.appId]" placeholder="请输入appId"></a-input>
        </a-form-item>
        <a-form-item label="appKey" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appKey', validatorRules.appKey]" placeholder="请输入appKey"></a-input>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'productRemark', validatorRules.productRemark]" placeholder="请输入备注"></a-input>
        </a-form-item>
<!--        <a-form-item label="url" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
<!--          <a-input v-decorator="[ 'url', validatorRules.url]" placeholder="请输入url"></a-input>-->
<!--        </a-form-item>-->

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
    name: "BroadbandProductModal",
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
          productSimpleName: {rules: [
          ]},
          productName: {rules: [
          ]},
          agentId: {rules: [
          ]},
          packageName: {rules: [
          ]},
          belongArea: {rules: [
          ]},
          packageCode: {rules: [
          ]},
          devStaffNum: {rules: [
          ]},
          depositNum: {rules: [
          ]},
          productRemark: {rules: [
          ]},
          flowType: {rules: [
          ]},
          privateKey: {rules: [
          ]},
          appId: {rules: [
          ]},
          appKey: {rules: [
          ]},
          expenseExplain: {rules: [
          ]},
          orderSource: {rules: [
          ]},
          distributionChannel: {rules: [
          ]},
          url: {rules: [
          ]},
          delFlag: {rules: [
          ]},
          createBy: {rules: [
          ]},
          createTime: {rules: [
          ]},
        },
        url: {
          add: "/product/broadbandProduct/add",
          edit: "/product/broadbandProduct/edit",
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
          this.form.setFieldsValue(pick(this.model,'productSimpleName','productName','agentId','packageName','belongArea','packageCode','devStaffNum','depositNum','productRemark','flowType','privateKey','appId','appKey','expenseExplain','orderSource','distributionChannel','url','delFlag','createBy','createTime'))
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
        this.form.setFieldsValue(pick(row,'productSimpleName','productName','agentId','packageName','belongArea','packageCode','devStaffNum','depositNum','productRemark','flowType','privateKey','appId','appKey','expenseExplain','orderSource','distributionChannel','url','delFlag','createBy','createTime'))
      },

      
    }
  }
</script>