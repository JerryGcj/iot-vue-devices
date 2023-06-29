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
      <a-form-item label="每日限额" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <a-form-item name="input-number" no-style>
          <a-input-number size="large" v-decorator="[ 'limitCount', validatorRules.limitCount]" :min="0" />
        </a-form-item>
      </a-form-item>
    </a-form>
  </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from "lodash.pick";

    export default {
        name: "EditLimitCountModal",
        data () {
            return {
              form: this.$form.createForm(this),
              confirmLoading: false,
              title:"配置每日限额",
              width:400,
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
              validatorRules: {
                limitCount: {

                },
              },
              url: {
                edit: "/electronchanneluser/electronChannelUser/edit",
              }
            }
        },
      methods: {
        edit (record) {
          console.log(record)
          this.form.resetFields();
          this.model = Object.assign({}, record);
          this.visible = true;
          this.model.visible = true;
          this.$nextTick(() => {
            this.form.setFieldsValue(pick(this.model,"limitCount"))
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
              formData.cancelMsg = ""
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
          this.form.setFieldsValue(pick(row,'agentName','packageCode','rakeBackTag'))
        },
      }
    }
</script>

<style scoped>

</style>