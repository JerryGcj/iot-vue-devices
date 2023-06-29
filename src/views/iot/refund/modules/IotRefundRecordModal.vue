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

        <a-form-item label="审核状态" :labelCol="labelCol" :wrapperCol="wrapperCol" >
          <a-select v-model="refundStatus" v-decorator="[ 'refundStatus', validatorRules.refundStatus]" placeholder="-请选择-">
            <a-select-option value="1">通过</a-select-option>
            <a-select-option value="2">驳回</a-select-option>
          </a-select>
        </a-form-item>

        <a-form-item v-if="refundStatus=='1'" label="退款金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'actualRefundMoney', validatorRules.actualRefundMoney]" placeholder="请输入金额"></a-input-number>
        </a-form-item>

        <a-form-item v-if="refundStatus=='1'&&this.isNew==0"
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol"
                     label="后续流程自动化">
          <a-switch checkedChildren="是" unCheckedChildren="否" @change="openSwitch" v-model="this.automation" ></a-switch>
        </a-form-item>
        <!--<a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="注">
          <span>1、未输入退款金额时默认实际退款金额即为申请的退款金额，若为其它金额请在上方输入</span>
        </a-form-item>-->
        <a-form-item label="退款说明" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea :rows="rows" v-decorator="[ 'refundMsg', validatorRules.refundMsg]" placeholder="请输入退款说明"></a-textarea>
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
    name: "IotRefundRecordModal",
    components: {
      JDate,
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
        automation: 0,
        isNew: 1,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        rows:4,
        confirmLoading: false,
        validatorRules: {
          refundStatus: {rules: [
              {
                required: true, message: '请选择审核状态!'
              }
          ]},
          actualRefundMoney: {rules: [
              {
                required: true, message: '请输入退款金额!'
              }
          ]},
          refundMsg: {rules: [
          ]},
          createTime: {rules: [
          ]},
        },
        url: {
          add: "/refund/iotRefundRecord/auditRefundRecord",
          edit: "/refund/iotRefundRecord/auditRefundRecord",
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
        this.isNew = record.isNew;
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          //this.form.setFieldsValue(pick(this.model,'openId','refundStatus','refundMsg','createTime'))
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
               method = 'post';
            }
            let formData = Object.assign(this.model, values);
            formData.automation = this.automation;
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,"post").then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
                that.loadData();
              }else{
                that.$message.warning(res.message);
                that.$emit('ok');
                that.loadData();
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
        //this.form.setFieldsValue(pick(row,'openId','refundStatus','refundMsg','createTime'))
      },
      openSwitch(checked){
        if(checked){
          this.automation = 1;
        }else{
          this.automation = 0;
        }
      },

    }
  }
</script>