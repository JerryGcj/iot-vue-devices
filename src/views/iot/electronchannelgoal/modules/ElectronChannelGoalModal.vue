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

        <a-form-item v-if="state" label="代理商名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag placeholder="-请选择-" :triggerChange="true" v-decorator="[ 'agentSimpleName', validatorRules.agentSimpleName_dictText]" dict-code="agent_name"></j-dict-select-tag>
        </a-form-item>
        <a-form-item v-if="state" label="目标月份" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-month-picker  v-decorator="[ 'goalDate', validatorRules.goalDate]"   placeholder="请输入目标月份"></a-month-picker>
        </a-form-item>
        <a-form-item label="销售目标数量" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'saleGoalCount', validatorRules.saleGoalCount]" placeholder="请输入销售目标数量" :min="0" ></a-input-number>
        </a-form-item>
        <a-form-item label="销售完成数量" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'saleCompleteCount', validatorRules.saleCompleteCount]" placeholder="请输入销售完成数量" :min="0" ></a-input-number>
        </a-form-item>
        <a-form-item label="激活目标数量" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'activeGoalCount', validatorRules.activeGoalCount]" placeholder="请输入激活目标数量" :min="0" ></a-input-number>
        </a-form-item>
        <a-form-item label="激活完成数量" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'activeCompleteCount', validatorRules.activeCompleteCount]" placeholder="请输入激活完成数量" :min="0" ></a-input-number>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input placeholder="请输入备注"></a-input>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from 'moment'

  export default {
    name: "ElectronCodeModal",
    components: {
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
        state: true,
        goalDate: null,
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
          agentSimpleName: {rules: [{ required: true, message: '请输入代理商简称!' }]},
          agentName: {rules: [{ required: true, message: '请输入代理商名称!' }]},
          goalDate: {rules: [{ required: true, message: '请输入目标月份!' }]},
          saleGoalCount: {rules: [{ required: true, message: '请输入销售目标数量!' }]},
          saleCompleteCount: {rules: [{ required: true, message: '请输入销售完成数量!' }]},
          remark: {rules: [{ required: true, message: '请输入备注!' }]},
          activeGoalCount: {rules: [{ required: true, message: '请输入激活目标数量!' }]},
          activeCompleteCount: {rules: [{ required: true, message: '请输入激活完成数量!' }]},
        },
        url: {
          add: "/electronchannelgoal/electronChannelGoal/add",
          edit: "/electronchannelgoal/electronChannelGoal/edit",
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
          this.form.setFieldsValue(pick(this.model,'agentSimpleName','saleGoalCount','saleCompleteCount','remark','activeGoalCount','activeCompleteCount'))
          //时间格式化
          this.form.setFieldsValue({goalDate:this.model.goalDate?moment(this.model.goalDate):null})

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
        this.form.setFieldsValue(pick(row,'agentSimpleName','goalDate','saleGoalCount','saleCompleteCount','remark','activeGoalCount','activeCompleteCount'))
      },

      
    }
  }
</script>