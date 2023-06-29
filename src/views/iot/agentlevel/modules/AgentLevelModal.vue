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

        <a-form-item label="等级名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'levelName', validatorRules.levelName]" placeholder="请输入等级名称"></a-input>
        </a-form-item>
        <a-form-item label="套餐返现比例(%)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'commissionRatio', validatorRules.commissionRatio]" placeholder="请输入套餐返现比例"></a-input>
        </a-form-item>
        <a-form-item label="提货返现金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'equipmentRatio', validatorRules.equipmentRatio]" placeholder="请输入提货返现金额"></a-input>
        </a-form-item>
        <a-form-item label="等级描述" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea placeholder="请输入等级描述" v-decorator="['note', validatorRules.note]" />
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
    name: "AgentLevelModal",
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
          levelName: {rules: [
          ]},
          commissionRatio: {rules: [
          ]},
          note: {rules: [
          ]},
          createTime: {rules: [
          ]},
          createBy: {rules: [
          ]},
        },
        url: {
          add: "/agentlevel/agentLevel/add",
          edit: "/agentlevel/agentLevel/edit",
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
          this.form.setFieldsValue(pick(this.model,'levelName','commissionRatio','equipmentRatio','note'))
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
        this.form.setFieldsValue(pick(row,'levelName','commissionRatio','equipmentRatio','note'))
      },


    }
  }
</script>