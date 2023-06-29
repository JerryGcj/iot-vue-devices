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

        <a-form-item label="省编码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'provinceCode', validatorRules.provinceCode]" placeholder="请输入省编码"></a-input>
        </a-form-item>
        <a-form-item label="省份" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'province', validatorRules.province]" placeholder="请输入省份"></a-input>
        </a-form-item>
        <a-form-item label="市编码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cityCode', validatorRules.cityCode]" placeholder="请输入市编码"></a-input>
        </a-form-item>
        <a-form-item label="市" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'city', validatorRules.city]" placeholder="请输入市"></a-input>
        </a-form-item>
        <a-form-item label="区编码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'countyCode', validatorRules.countyCode]" placeholder="请输入区编码"></a-input>
        </a-form-item>
        <a-form-item label="区" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'county', validatorRules.county]" placeholder="请输入区"></a-input>
        </a-form-item>

          <a-form-item label="类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
            <a-select v-decorator="[ 'type', validatorRules.type]" placeholder="请选择类型" allowClear>
              <a-select-option value="jiangsu-unicom">jiangsu-unicom</a-select-option>
              <a-select-option value="taizhou-telecom">taizhou-telecom</a-select-option>
            </a-select>
          </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'

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
          provinceCode: {rules: [{ required: true, message: '请输入省编码!' }]},
          province: {rules: [{ required: true, message: '请输入省份!' }]},
          cityCode: {rules: [{ required: true, message: '请输入市编码!' }]},
          city: {rules: [{ required: true, message: '请输入市!' }]},
          countyCode: {rules: [{ required: true, message: '请输入区编码!' }]},
          county: {rules: [{ required: true, message: '请输入区!' }]},
          type: {rules: [{ required: true, message: '请选择类型!' }]},
        },
        url: {
          add: "/electroncode/electronCode/add",
          edit: "/electroncode/electronCode/edit",
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
          this.form.setFieldsValue(pick(this.model,'provinceCode','province','cityCode','city','countyCode','county',"type"))
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
        this.form.setFieldsValue(pick(row,'provinceCode','province','cityCode','city','countyCode','county',"type"))
      },


    }
  }
</script>