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

        <a-form-item label="集成电路卡识别码20位字符" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'iccid', validatorRules.iccid]" placeholder="请输入集成电路卡识别码20位字符"></a-input>
        </a-form-item>
        <a-form-item label="统计时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择统计时间" v-decorator="[ 'date', validatorRules.date]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="流量使用量（MB）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'cardUsage', validatorRules.cardUsage]" placeholder="请输入流量使用量（MB）" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="创建日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择创建日期" v-decorator="[ 'createDate', validatorRules.createDate]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="上次同步时间更新时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择上次同步时间更新时间" v-decorator="[ 'updateDate', validatorRules.updateDate]" :trigger-change="true" style="width: 100%"/>
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
    name: "IotUnicomCardUsageModal",
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
          date: {rules: [
          ]},
          cardUsage: {rules: [
          ]},
          createDate: {rules: [
          ]},
          updateDate: {rules: [
          ]},
        },
        url: {
          add: "/unicomcardusage/iotUnicomCardUsage/add",
          edit: "/unicomcardusage/iotUnicomCardUsage/edit",
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
          this.form.setFieldsValue(pick(this.model,'iccid','date','cardUsage','createDate','updateDate'))
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
        this.form.setFieldsValue(pick(row,'iccid','date','cardUsage','createDate','updateDate'))
      },

      
    }
  }
</script>