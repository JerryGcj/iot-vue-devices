<template>
  <a-modal
    :title="title"
    :width="600"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="运营商">
          <j-dict-select-tag v-decorator="[ 'operationId', validatorRules.operationId]" :triggerChange="true" placeholder="请选择运营商" style="width: 80%"
                dictCode="electron_operator_config,operator,id"/>
        </a-form-item>

        <a-form-item
                 :labelCol="labelCol"
                 :wrapperCol="wrapperCol"
                 label="月份">
          <a-month-picker placeholder="请选择月份"  v-decorator="[ 'month', validatorRules.month]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="平均在网月份">
          <a-input-number placeholder="请输入平均在网月份" v-decorator="['avgActiveMonth', validatorRules.avgActiveMonth]" />
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction,downFile } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import {queryOperator,cardImportExcel,queryLowerAgent } from '@/api/api'
  import { postAction } from '@/api/manage'

  export default {
    name: "ElectronOperationIncomeImport",
    data () {
      return {
        title:"操作",
        visible: false,
        model: {},
        fileList:[],

        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },

        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
          operationId:{
            rules: [{
              required: true, message: '请选择运营商!'
            }]
          },
          month:{
            rules: [{
              required: true, message: '请选择月份!'
            }]
          }
        },
        url: {
          createOverall: "/electronoperationoverall/electronOperationOverall/createOverall"
        },
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
		  //时间格式化

        });

      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if(!err){
            that.confirmLoading = true;
            let formData = Object.assign(this.model, values);
            postAction(that.url.createOverall, formData).then((res) => {
              if(res.success){
                that.$message.success(res.message)
                that.visible=false
                that.$emit('ok')
              }else{
                that.$message.warning(res.message)
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
      }
    }
  }
</script>
