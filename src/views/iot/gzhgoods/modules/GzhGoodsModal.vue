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


        <a-form-item v-show="showSelect" label="选择产品" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select
            allowClear
            show-search
            v-model="operatorValue"
            style="width: 100%"
            placeholder="请选择"
            :options="dictOperatorOptions"
            :filterOption="likeQuery"
            :autoClearSearchValue="false"
            :maxTagTextLength=3
            :maxTagCount=3
          ></a-select>
        </a-form-item>

          <a-form-item v-show="showSelect" label="用户" :labelCol="labelCol" :wrapperCol="wrapperCol">
            <a-select
              allowClear
              show-search
              v-model="selectValue"
              style="width: 100%"
              placeholder="请选择"
              :options="dictOptions"
              :filterOption="likeQuery"
              :autoClearSearchValue="false"
              :maxTagTextLength=3
              :maxTagCount=3
            ></a-select>
          </a-form-item>

        <a-form-item label="产品标题" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'title', validatorRules.title]" placeholder="请输入产品标题"></a-input>
        </a-form-item>

        <a-form-item label="公众号" :labelCol="labelCol" :wrapperCol="wrapperCol">
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

        <a-form-item label="状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'status', validatorRules.status]" placeholder="请输入状态">
            <a-select-option value="0">上架</a-select-option>
            <a-select-option value="1">下架</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="排序" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'sort', validatorRules.sort]" placeholder="请输入排序" style="width: 100%"/>
        </a-form-item>
        <a-form-item v-show="show" label="点击跳转页面地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea v-decorator="[ 'redirectUrl', validatorRules.redirectUrl]" placeholder="请输入点击跳转页面地址"></a-textarea>
        </a-form-item>

        <a-form-item v-show="showSelect" label="上传图片" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-upload
            name="file"
            :multiple="true"
            accept=".jpg,.png,.jpeg"
            :fileList="fileList"
            :remove="handleRemove"
            :beforeUpload="beforeUpload">
            <a-button>
              <a-icon type="upload" />
              点击上传
            </a-button>
          </a-upload>
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
  import { postAction } from '@/api/manage'
  import {putAction} from "../../../../api/manage";


  export default {
    name: "GzhGoodsModal",
    components: {
      JDate,
    },
    data () {
      return {
        fileList:[],
        dictOptionsMch:{},
        appId:"",
        selectValue:"",
        operatorValue: "",
        dictOperatorOptions:[],
        show: undefined,
        showSelect: undefined,
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
          agentId: {rules: [
          ]},
          title: {rules: [{required: true, message: '请输入标题!'}]},
          status: {rules: [
          ]},
          sort: {rules: [
          ]},
          imageUrl: {rules: [
          ]},
          createBy: {rules: [
          ]},
          createTime: {rules: [
          ]},
          updateBy: {rules: [
          ]},
          updateTime: {rules: [
          ]},
          redirectUrl: {rules: []},
          appId: {rules: [{required: true, message: '请输入公众号id!'}]},
        },
        url: {
          add: "/gzhgoods/gzhGoods/add",
          edit: "/gzhgoods/gzhGoods/edit",
          initOperatorUrl: "/electronchannelagent/electronChannelAgent/initOperatorByDel",
          initAgentUrl: "/sys/user/initUserCompany",
          initMchUrl: "/wechatpay/iotWechatPay/initMchNameCompany",

        }
      }
    },
    created () {
      this.initOperator();
      this.initAgent();
      this.validatorRules.agentId = ""
      this.initMch();
    },
    methods: {
      handleRemove(file) {
        const index = this.fileList.indexOf(file);
        const newFileList = this.fileList.slice();
        newFileList.splice(index, 1);
        this.fileList = newFileList
      },
      beforeUpload(file) {
        this.fileList = [...this.fileList, file];
        return false;
      },
      // handleImport() {
      //   const { fileList } = this;
      //   const formData = new FormData();
      //   fileList.forEach((file) => {
      //     formData.append('files[]', file);
      //     formData.append('id',this.id);
      //   });
      //   console.log(formData.get("id"))
      //   this.uploading = true;
      //   postAction("/gzhgoods/gzhGoods/uploadPicture", formData,this.id).then((res) => {
      //     this.uploading = false;
      //     if(res.success){
      //       this.$message.success(res.message);
      //       this.visible=false;
      //       this.$emit('ok')
      //     }else{
      //       this.$message.warning(res.message)
      //     }
      //   })
      // },
      getPopupContainer(node) {
        if (node) {
          return node.parentNode
        }
        return document.body
      },
      add () {
        this.edit({},false);
      },
      edit (record,show) {
        if (show == undefined) {
          this.show = true
          this.showSelect = false
        } else {
          this.show = false
          this.showSelect = true
        }
        if (this.showSelect) {
          this.appId = ""
        } else {
          this.appId = record.appId
        }
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'agentId','title','status','sort','imageUrl','createBy','createTime','updateBy','updateTime','redirectUrl','userId','appId'))
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
        this.selectValue="";
        this.operatorValue= "";
         this.fileList = []
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
            let a = Object.assign(this.model, values);
            console.log("model数据",a);
            // if (this.operatorValue != "" && this.selectValue != "") {
            //   formData["agentId"] = this.operatorValue;
            //   formData["userId"] = this.selectValue;
            //   formData["appId"] = this.appId;
            // }
            const formData = new FormData();
            if (method === 'post') {
              this.fileList.forEach(file => {
                formData.append('files', file);
              })
            }
            if (this.operatorValue != "" && this.selectValue != "") {
              formData.append("agentId",this.operatorValue)
              formData.append("userId",this.selectValue)
              formData.append("appId",this.appId)
            }
            formData.append("title",this.model.title)
            formData.append("id",this.model.id)
            formData.append("status",this.model.status)
            formData.append("userId",this.model.userId)
            formData.append("redirectUrl",this.model.redirectUrl)
            formData.append("sort",this.model.sort.toString())


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
        this.show = null
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'agentId','title','status','sort','imageUrl','createBy','createTime','updateBy','updateTime','redirectUrl','userId','appId'))
      },
      initOperator() {
        getAction(this.url.initOperatorUrl).then((res) => {
          if (res.success) {
            this.dictOperatorOptions = res.result
          }
        })
      },
      likeQuery(input, option){
        return (option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0)
      },
      initAgent(selectValue){
        let params={selectValue:selectValue};
        getAction(this.url.initAgentUrl,params).then((res)=>{
          if(res.success){
            this.dictOptions = res.result;
          }
        })

      },
      initMch(){
        getAction(this.url.initMchUrl).then((res)=>{
          if(res.success){
            this.dictOptionsMch = res.result;
          }
        })

      },
    }
  }
</script>