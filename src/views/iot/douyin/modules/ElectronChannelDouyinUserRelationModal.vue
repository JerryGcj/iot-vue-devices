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

        <a-form-item label="达人昵称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'dyName', validatorRules.dyName]" placeholder="请输入达人昵称"></a-input>
        </a-form-item>
        <a-form-item label="达人ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'dyId', validatorRules.dyId]" placeholder="请输入达人ID"></a-input>
        </a-form-item>
        <a-form-item label="用户表ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'ourId', validatorRules.ourId]" placeholder="请输入用户表ID"></a-input>
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
  import {duplicateCheck} from "@api/api";

  export default {
    name: "ElectronChannelDouyinUserRelationModal",
    components: { 
      JDate,
    },
    data () {
      return {
        userId:'',
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
          dyName: {rules: [
              { required: true, message: '请输入达人昵称!' }
          ]},
          dyId: {rules: [
              { required: true, message: '请输入达人ID!' },{
                validator: this.validateDyId,
              }
          ]},
          ourId: {rules: [
              { required: true, message: '请输入用户表ID!' }
          ]},

        },
        url: {
          add: "/douyin/electronChannelUserRelation/add",
          edit: "/douyin/electronChannelUserRelation/edit",
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
          this.form.setFieldsValue(pick(this.model,'dyName','dyId','ourId','createTime','createBy','updateTime','updateBy'))
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
      validateDyId(rule, value, callback){
        var params = {
          tableName: 'electron_channel_douyin_user_relation',
          fieldName: 'dy_id',
          fieldVal: value,
          dataId: this.userId
        };
        duplicateCheck(params).then((res) => {
          if (res.success) {
            callback()
          } else {
            callback("此达人ID已维护!")
          }
        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'dyName','dyId','ourId','createTime','createBy','updateTime','updateBy'))
      },

      
    }
  }
</script>