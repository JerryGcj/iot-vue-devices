<template>
  <a-modal
    title="生成二维码"
    :width="600"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleSubmit"
    @cancel="handleCancel"
    cancelText="关闭"
    style="top:20px;"
  >
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="是否带语音功能" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'ifvoice', validatorRules.ifvoice]"  placeholder="请选择是否带语音功能">
            <a-select-option value="0">否</a-select-option>
            <a-select-option value="1">是</a-select-option>
          </a-select>
        </a-form-item>

      </a-form>
    </a-spin>
    <electron-qr-code-modal ref="qrCodeModal"></electron-qr-code-modal>
  </a-modal>
</template>

<script>
  import ElectronQrCodeModal from './ElectronQrCodeModal'
  import { getAction } from '@/api/manage'

  export default {
    name: "PasswordModal",
    components: {
      ElectronQrCodeModal
    },
    data () {
      return {
        visible: false,
        confirmLoading: false,
        confirmDirty: false,
        id: '',
        realname: '',
        validatorRules:{
          ifvoice:{
            rules: [{
              required: true, message: '请选择是否带语音功能!',
            }],
          },
        },

        model: {},

        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 8 },
        },
        form:this.$form.createForm(this)
      }
    },
    created () {
      console.log("created");
    },

    methods: {
      show (record) {
        this.form.resetFields();
        this.visible = true;
        this.realname = record.realname;
        this.id = record.id;
        this.$nextTick(() => {
          this.form.setFieldsValue({username:username});
        });

      },
      close () {
        this.$emit('close');
        this.visible = false;
        this.disableSubmit = false;
        this.selectedRole = [];
      },
      handleSubmit () {
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            this.confirmLoading = true;
            this.$refs.qrCodeModal.visible = true;
            this.$refs.qrCodeModal.title = "电渠代理商【"+this.realname+"】二维码";
            this.$refs.qrCodeModal.show(this.id,values.ifvoice);
            this.confirmLoading = false;
            this.close();
            /*this.confirmLoading = true;
            let formData = Object.assign(this.model, values);
            getAction(formData).then((res)=>{
              if(res.success){
                this.$message.success(res.message);
                this.$emit('ok');
              }else{
                this.$message.warning(res.message);
              }
            }).finally(() => {
              this.confirmLoading = false;
              this.close();
            });*/
          }
        })
      },
      handleCancel () {
        this.close()
      }
    }
  }
</script>