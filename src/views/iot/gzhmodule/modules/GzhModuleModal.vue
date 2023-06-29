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

        <a-form-item v-show="show" label="公众号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select
            :getPopupContainer="getPopupContainer"
            allowClear
            show-search
            v-model="appId"
            style="width: 100%"
            placeholder="请选择"
            :options="this.dictOptionsMch"
            :filterOption="likeQuery"
            :autoClearSearchValue="false"
            :maxTagTextLength=3
            :maxTagCount=3
          ></a-select>
        </a-form-item>

<!--        <a-form-item label="公众号ID" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
<!--          <a-input v-decorator="[ 'appId', validatorRules.appId]" placeholder="请输入公众号ID"></a-input>-->
<!--        </a-form-item>-->
        <a-form-item label="模块标题" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'title', validatorRules.title]" placeholder="请输入模块标题"></a-input>
        </a-form-item>

        <a-form-item label="状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'status', validatorRules.status]" placeholder="请输入状态">
            <a-select-option value="0">上架</a-select-option>
            <a-select-option value="1">下架</a-select-option>
          </a-select>
        </a-form-item>

        <a-form-item label="排序" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'sort', validatorRules.sort]" placeholder="请输入排序" style="width: 100%"/>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction,getAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JDate from '@/components/jeecg/JDate'


  export default {
    name: "GzhModuleModal",
    components: { 
      JDate,
    },
    data () {
      return {
        show:undefined,
        appId: "",
        dictOptionsMch:{},
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
          appId: {rules: [
          ]},
          title: {rules: [
          ]},
          status: {rules: [
          ]},
          sort: {rules: [
          ]},
          createBy: {rules: [
          ]},
          createTime: {rules: [
          ]},
          updateBy: {rules: [
          ]},
          updateTime: {rules: [
          ]},
        },
        url: {
          add: "/gzhmodule/gzhModule/add",
          edit: "/gzhmodule/gzhModule/edit",
          initMchUrl: "/wechatpay/iotWechatPay/initMchNameCompany",
        }
      }
    },
    created () {
      this.initMch();

    },
    methods: {
      likeQuery(input, option){
        return (option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0)
      },
      initMch(){
        getAction(this.url.initMchUrl).then((res)=>{
          if(res.success){
            this.dictOptionsMch = res.result;
          }
        })

      },
      add () {
        this.appId = ""
        this.edit({},true);
      },
      edit (record,show) {
        if (show == undefined){
          this.show = false
        } else {
          this.show = true
        }
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'appId','title','status','sort','createBy','createTime','updateBy','updateTime'))
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
            debugger
            let formData = Object.assign(this.model, values);
            if (!formData.appId) {
              formData["appId"] = this.appId
            }
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
        this.form.setFieldsValue(pick(row,'appId','title','status','sort','createBy','createTime','updateBy','updateTime'))
      },

      
    }
  }
</script>