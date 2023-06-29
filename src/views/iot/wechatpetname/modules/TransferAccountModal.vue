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

        <a-form-item label="旧手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'mobile', {}]" placeholder="" disabled></a-input>
        </a-form-item>

        <a-form-item label="新手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'newMobile', validatorRules.newMobile]" placeholder="请输入新账户绑定的手机号"></a-input>
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
    name: "ChangeCardModal",
    components: {
      JDate,
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:500,
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
        rows:4,
        confirmLoading: false,
        validatorRules: {
          newMobile: {rules: [
              {
                required: true, message: '请输入新手机号!'
              }
          ]},
          refundMsg: {rules: [
          ]},
          createTime: {rules: [
          ]},
        },
        url: {
          add: "/wechatpetname/iotCardWechatRelation/transferAccount",
          edit: "/wechatpetname/iotCardWechatRelation/transferAccount",
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
          this.form.setFieldsValue(pick(this.model,'mobile','newMobile'))
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
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,"put").then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
                that.close();
                that.loadData();
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
            })
          }

        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'mobile','newMobile'))
      }

    }
  }
</script>