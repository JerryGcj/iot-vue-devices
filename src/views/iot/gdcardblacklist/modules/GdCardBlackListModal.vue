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

        <a-form-item label="商品编码" :labelCol="labelCol" :wrapperCol="wrapperCol">
<!--          <a-input v-decorator="[ 'agentId', validatorRules.agentId]" placeholder="请输入商品编码"></a-input>-->
          <j-search-select-tag
            placeholder="请选择商品"
            v-model="agentId"
            dict="electron_channel_agent,agent_name,id"
            :async="true">
          </j-search-select-tag>
        </a-form-item>
        <a-form-item label="客户姓名" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusName', validatorRules.cusName]" placeholder="请输入客户姓名"></a-input>
        </a-form-item>
        <a-form-item label="客户手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusPhone', validatorRules.cusPhone]" placeholder="请输入客户手机号"></a-input>
        </a-form-item>
        <a-form-item label="客户身份证号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusIdno', validatorRules.cusIdno]" placeholder="请输入客户身份证号"></a-input>
        </a-form-item>
        <a-form-item label="详细地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'detailAddr', validatorRules.detailAddr]" placeholder="请输入详细地址"></a-input>
        </a-form-item>
<!--        <a-form-item label="是否开启过滤" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
<!--          <a-switch checkedChildren="是" unCheckedChildren="否" v-model="filterFlag"/>-->
<!--        </a-form-item>-->
        <a-form-item label="进入黑名单原因" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea v-decorator="[ 'filterMsg', validatorRules.filterMsg]" placeholder="请输入进入黑名单原因"></a-textarea>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import JDate from '@/components/jeecg/JDate'  

  export default {
    name: "GdCardBlackListModal",
    components: { 
      JDate,
      JSearchSelectTag
    },
    data () {
      return {
        filterFlag: false,
        agentId: '',
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
          agentId: {rules: [
          ]},
          cusName: {rules: [
          ]},
          cusPhone: {rules: [
          ]},
          cusIdno: {rules: [
          ]},
          payerClientIp: {rules: [
          ]},
          province: {rules: [
          ]},
          city: {rules: [
          ]},
          district: {rules: [
          ]},
          detailAddr: {rules: [
          ]},
          filterFlag: {rules: [
          ]},
          filterMsg: {rules: [
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
          add: "/gdcardblacklist/gdCardBlackList/add",
          edit: "/gdcardblacklist/gdCardBlackList/edit",
        }
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
        this.agentId = ""
      },
      edit (record) {
        this.filterFlag = !record.filterFlag?false:true;
        if (record.filterFlag === false) {
          this.filterFlag = "0"
        } else if (record.filterFlag === true) {
          this.filterFlag = "1"
        }
        this.agentId = record.agentId;
        this.form.resetFields();
        this.model = Object.assign({}, record);
        console.log(this.model)
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'agentId','cusName','cusPhone','cusIdno','payerClientIp','province','city','district','detailAddr','filterFlag','filterMsg','createBy','createTime','updateBy','updateTime'))
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
          if (!err)
          this.model.filterFlag = this.filterFlag;
          this.model.agentId = this.agentId;
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
        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'agentId','cusName','cusPhone','cusIdno','payerClientIp','province','city','district','detailAddr','filterFlag','filterMsg','createBy','createTime','updateBy','updateTime'))
      },

      
    }
  }
</script>