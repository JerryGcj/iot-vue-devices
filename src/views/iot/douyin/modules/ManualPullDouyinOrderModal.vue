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
        <a-form-item label="抖音订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea :rows="rows" v-decorator="[ 'orderId', validatorRules.orderId]" placeholder="请输入抖音订单号"></a-textarea>
        </a-form-item>
        <a-form-item label="抖店店铺" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag  v-decorator="[ 'shopId', validatorRules.shopId]" placeholder="请选择店铺" dict-code="doudian_shop" :trigger-change="true"></j-dict-select-tag>
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="说明" help="">
        <span style="color: red">自抖店下单时间起，超过48小时的订单已无法拉取。</span>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import ATextarea from "ant-design-vue/es/input/TextArea";
  import JDate from '@/components/jeecg/JDate'

  export default {
    name: "ElectronChannelOrderModal",
    components: {
      JDate,
      ATextarea
    },
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
          orderId: {
            rules: [
              { required: true, message: '请输入订单号!'}
            ]
          },
          shopId: {
            rules: [
              { required: true, message: '请选择店铺!'}
            ]
          },
        },
        url: {
          add: "/douyin/electronChannelDouyinOrder/manualPullOrder",
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
            let formData = Object.assign(this.model, values);
            httpAction(this.url.add+"/"+formData.shopId+"/"+formData.orderId,'','post').then((res)=>{
              if(res.success){
                that.$message.success(res.message);
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
        this.form.setFieldsValue(pick(row,'orderId'))
      },


    }
  }
</script>