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

        <a-form-item label="客户姓名" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusName', validatorRules.cusName]" placeholder="请输入客户姓名"></a-input>
        </a-form-item>
        <a-form-item label="客户手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusPhone', validatorRules.cusPhone]" placeholder="请输入客户手机号"></a-input>
        </a-form-item>
        <a-form-item label="身份证号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusIdno', validatorRules.cusIdno]" placeholder="请输入身份证号"></a-input>
        </a-form-item>
        <a-form-item label="省" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'province', validatorRules.province]" placeholder="请输入省"></a-input>
        </a-form-item>
        <a-form-item label="市" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'city', validatorRules.city]" placeholder="请输入市"></a-input>
        </a-form-item>
        <a-form-item label="区" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'district', validatorRules.district]" placeholder="请输入区"></a-input>
        </a-form-item>
        <a-form-item label="详细地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'detailAddr', validatorRules.detailAddr]" placeholder="请输入详细地址"></a-input>
        </a-form-item>
        <a-form-item label="订单状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag
            :triggerChange="true"
            v-decorator="[ 'orderStatus', validatorRules.orderStatus]"
            dictCode="broadband_order_status"
            placeholder="请选择订单状态">
          </j-dict-select-tag>
        </a-form-item>
        <a-form-item label="宽带产品" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag
            :triggerChange="true"
            v-decorator="[ 'productId', validatorRules.productId]"
            dictCode="broadband_product,product_name,id"
            placeholder="请选择销售产品">
          </j-dict-select-tag>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@api/manage'
  import pick from 'lodash.pick'
  import JDate from '@comp/jeecg/JDate'
  import JDictSelectTag from '@/components/dict/JDictSelectTag'
  export default {
    name: "BroadbandOrderModal",
    components: { 
      JDate,JDictSelectTag
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
          cusId: {rules: [
          ]},
          cusName: {
            rules: [
              { required: true, message: '请填写客户姓名!'}
            ]
          },
          cusPhone: {
            rules: [
              { required: true, message: '请填写客户手机号!'}
            ]
          },
          cusIdno: {rules: [
            ]},
          detailAddr: {
            rules: [
              { required: true, message: '请填写详细地址!'}
            ]
          },
          province: {rules: [
              { required: true, message: '请填写省份!'}
            ]},
          city: {rules: [
              { required: true, message: '请填写城市!'}
            ]},
          district: {rules: [
              { required: true, message: '请填写区(县)!'}
            ]},
          productId: {rules: [
              { required: true, message: '请选择产品!'}
            ]},
        },
        url: {
          add: "/broadbank/broadbandOrder/add",
          edit: "/broadbank/broadbandOrder/edit",
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
          this.form.setFieldsValue(pick(this.model,'cusId','cusName','cusPhone','cusIdno','province','city','district','detailAddr','productId','outTradeNo','channelName','orderStatus','commitTime','iccid','accessNumber','networkAccessTime','cancelMsg','smsStatus','orderNum','activationDate','provinceCode','cityCode','createBy','createTime','updateBy','updateTime'))
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
        this.form.setFieldsValue(pick(row,'cusId','cusName','cusPhone','cusIdno','province','city','district','detailAddr','productId','outTradeNo','channelName','orderStatus','commitTime','iccid','accessNumber','networkAccessTime','cancelMsg','smsStatus','orderNum','activationDate','provinceCode','cityCode','createBy','createTime','updateBy','updateTime'))
      },

      
    }
  }
</script>