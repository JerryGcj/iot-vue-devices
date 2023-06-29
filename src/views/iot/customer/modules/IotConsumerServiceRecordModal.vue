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

        <!--<a-form-item label="解决状态" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
          <!--<j-dict-select-tag placeholder="请选择解决状态" v-decorator="['solveStatus', validatorRules.solveStatus]" dict-code="solve_status"></j-dict-select-tag>-->
        <!--</a-form-item>-->
        <a-form-item label="处理备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea v-decorator="['solveRemark', validatorRules.solveRemark]" rows="4" placeholder="请输入解决备注"/>
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
    name: "IotConsumerServiceRecordModal",
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
          openId: {rules: [
          ]},
          tagId: {rules: [
          ]},
          content: {rules: [
          ]},
          solveStatus: {rules: [
              {required: true, message: '请选择解决状态'},
            ]},
          solveUser: {rules: [
          ]},
          solveRemark: {rules: [
          ]},
          updateTime: {rules: [
          ]},
        },
        url: {
          add: "/consumer/iotConsumerServiceRecord/add",
          edit: "/consumer/iotConsumerServiceRecord/edit",
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
        this.model.solveStatus = 1;
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'openId','tagId','content','solveTime','solveUser','solveRemark','updateTime'))
        });
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
        this.form.setFieldsValue(pick(row,'openId','tagId','content','solveStatus','solveTime','solveUser','solveRemark','updateTime'))
      },


    }
  }
</script>