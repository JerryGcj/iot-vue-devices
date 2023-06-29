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
          <a-input v-decorator="[ 'cusName', validatorRules.cusName]" placeholder="请输入客户姓名" disabled></a-input>
        </a-form-item>
        <a-form-item label="客户手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusPhone', validatorRules.cusPhone]" placeholder="请输入客户手机号" disabled></a-input>
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
          <a-textarea :rows="rows" v-decorator="[ 'detailAddr', validatorRules.detailAddr]" placeholder="请输入详细地址"></a-textarea>
        </a-form-item>
        <a-form-item label="发货状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'deliveryState', validatorRules.deliveryState]" placeholder="请选择">
            <a-select-option v-for="d in dictOptions" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="ICCID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'iccid', validatorRules.iccid]" placeholder="请输入ICCID"></a-input>
        </a-form-item>
        <a-form-item label="快递单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'expressNo', validatorRules.expressNo]" placeholder="请输入快递单号"></a-input>
        </a-form-item>
        <a-form-item label="快递公司" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <!--<a-input v-decorator="[ 'expressCompany', validatorRules.expressCompany]" placeholder="请输入快递公司"></a-input>-->
          <j-dict-select-tag v-decorator="[ 'expressId', validatorRules.expressId]" :triggerChange="true" placeholder="请选择快递公司" dictCode="courier_company,courier_name,id"/>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea :rows="rows" v-decorator="[ 'remark', {}]" placeholder="请输入备注"></a-textarea>
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
  import {ajaxGetDictItems} from '@/api/api'

  export default {
    name: "IotCardOrderModal",
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
          cusName: {rules: [
          ]},
          cusPhone: {rules: [
          ]},
          province: {rules: [
          ]},
          city: {rules: [
          ]},
          district: {rules: [
          ]},
          detailAddr: {rules: [
          ]},
          deliveryState: {rules: [{ required: true, message: '请选择发货状态!'}]},
          expressNo: {rules: [
              { required: true, message: '请输入快递单号!'}
          ]},
          expressId: {rules: [
              { required: true, message: '请选择快递公司!'}
          ]},
        },
        url: {
          add: "/cardorder/iotCardOrder/add",
          edit: "/cardorder/iotCardOrder/edit",
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
        this.initDictData();
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'cusName','cusPhone','province','city','district','detailAddr','deliveryState','remark','iccid','expressNo','expressId'))
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
      initDictData() {
        //根据字典Code, 初始化字典数组
        ajaxGetDictItems('unicom_order_state', null).then((res) => {
          if (res.success) {
            //console.log(res.result);
            this.dictOptions = res.result;
          }
        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'cusName','cusPhone','province','city','district','detailAddr','deliveryState','remark','iccid','expressNo','expressId'))
      },


    }
  }
</script>