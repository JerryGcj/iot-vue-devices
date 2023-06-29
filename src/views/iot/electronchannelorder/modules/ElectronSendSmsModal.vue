<template>
  <a-modal
    :title="title"
    :width="800"
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
          label="发送内容">
          <a-textarea placeholder="请输入短信内容"  :rows="4" v-model="smsContext"/>
        </a-form-item>

		
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"

  export default {
    name: "CardDistributionModal",
    data () {
      return {
        importBatch:'',
        smsContext:'【订单通知】亲，您订购的小白卡，${iccid}已经发货，快递单号 ${expressNo} 迫不及待的奔向你了，2-5天左右送到，收到后需尽快激活哦，如有未能正常收件，请及时与我们联系：15688885014',
        title:"操作",
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
        form: this.$form.createForm(this),
        validatorRules:{
        },
        url: {
          add: "/carddistribution/cardDistribution/add",
          edit: "/carddistribution/cardDistribution/edit",
        },
      }
    },
    created () {
    },
    methods: {
      add () {
        this.visible = true;
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
            let httpurl = '/electronchannelorder/electronChannelOrder/sendSms';
            let formData = Object.assign(this.model, values);
            formData['importBatch'] = this.importBatch;
            formData['smsContext'] = this.smsContext;
            console.log(formData);
            httpAction(httpurl,formData,'post').then((res)=>{
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


    }
  }
</script>

<style lang="less" scoped>

</style>