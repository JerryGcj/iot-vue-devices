<template>
  <a-modal
    :title="title"
    :width="width"
    :visible="visible"
    :confirmLoading="confirmLoading"
    :okButtonProps="{ props: {disabled: disableSubmit} }"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-card class="card"  :bordered="false">
          <a-row class="form-row" :gutter="20">
            <a-col :lg="10">
              <a-form-item label="ICCID" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-input disabled="" v-decorator="[ 'iccid', validatorRules.iccid ]"/>
              </a-form-item>
            </a-col>
            <a-col :lg="10">
              <a-form-item label="MSISDN" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-input disabled="" v-decorator="['msisdn', validatorRules.msisdn ]"/>
              </a-form-item>
            </a-col>
          </a-row>
          <a-row class="form-row" :gutter="20">
            <a-col :lg="10">
              <a-form-item label="真实姓名" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-input disabled="" v-decorator="['name', validatorRules.name ]" />
              </a-form-item>
            </a-col>
            <a-col :lg="10">
              <a-form-item label="身份证号" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-input disabled="" v-decorator="[ 'idCardNumber', validatorRules.idCardNumber]" />
              </a-form-item>
            </a-col>
          </a-row>
          <a-row class="form-row" :gutter="20">
            <a-col :lg="10">
              <a-form-item label="手机号码" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-input disabled="" v-decorator="['mobile', validatorRules.mobile ]" />
              </a-form-item>
            </a-col>
            <a-col :lg="10">
              <a-form-item label="商户名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-input disabled="" v-decorator="[ 'userCompany', validatorRules.userCompany]" />
              </a-form-item>
            </a-col>
          </a-row>
          <a-row class="form-row" :gutter="20">
            <a-col :lg="10">
              <a-form-item label="创建时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-input disabled="" v-decorator="['createTime', validatorRules.createTime ]" />
              </a-form-item>
            </a-col>
            <a-col :lg="10">
              <a-form-item label="修改时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-input disabled="" v-decorator="[ 'updateTime', validatorRules.updateTime]" />
              </a-form-item>
            </a-col>
          </a-row>
          <a-row class="form-row" :gutter="20">
            <a-col :lg="10">
              <a-form-item label="请求流水号" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-input disabled="" v-decorator="['serialNumber', validatorRules.serialNumber ]" />
              </a-form-item>
            </a-col>
            <a-col :lg="10">
              <a-form-item label="审核状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-select v-decorator="[ 'status', {  initialValue: '1' }]" >
                  <a-select-option value="1">审核通过</a-select-option>
                  <a-select-option value="2">审核不通过</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
          </a-row>
          <a-row class="form-row" :gutter="20">
            <a-col :lg="10">
              <a-form-item label="审核备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
                <a-textarea v-decorator="['remark', {  initialValue: '人工审核' } ]" ></a-textarea>
              </a-form-item>
            </a-col>
          </a-row>
        </a-card>

        <a-crad>
          <a-row class="form-row" :gutter="16">
            <a-col :lg="8">
              <a-form-item label="身份证正面">
                <div v-for="(fileDetail,index) in dataSource[0].fileDetails" :key="index">
                    <div style="width: 100%;height: 100%;position: relative;padding: 8px;border: 0px solid #d9d9d9;border-radius: 4px;">
                      <img style="width: 100%;" :src="fileDetail.imgUrl"  :preview="dataSource[0].key">
                    </div>
                </div>
              </a-form-item>
            </a-col>
            <a-col :lg="8">
              <a-form-item label="身份证反面">
                <div v-for="(fileDetail,index) in dataSource[1].fileDetails" :key="index">
                  <div style="width: 100%;height: 100%;position: relative;padding: 8px;border: 0px solid #d9d9d9;border-radius: 4px;">
                    <img style="width: 100%;" :src="fileDetail.imgUrl"  :preview="dataSource[1].key">
                  </div>
                </div>
              </a-form-item>
            </a-col>
            <a-col :lg="8">
              <a-form-item label="手持身份证">
                <div v-for="(fileDetail,index) in dataSource[2].fileDetails" :key="index">
                  <div style="width: 100%;height: 100%;position: relative;padding: 8px;border: 0px solid #d9d9d9;border-radius: 4px;">
                    <img style="width: 100%;" :src="fileDetail.imgUrl"  :preview="dataSource[2].key">
                  </div>
                </div>
              </a-form-item>
            </a-col>
          </a-row>

        </a-crad>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  import { queryDetails } from '@/api/api'

  export default {
    name: "RealNameSystemEditModal",
    components: { 
      JDictSelectTag,
      queryDetails
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:1000,
        visible: false,
        model: {},
        dataSource: [],
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
          iccid: {rules: [
          ]},
          msisdn: {rules: [
          ]},
          name: {rules: [
          ]},
          idCardNumber: {rules: [
          ]},
          mobile: {rules: [
          ]},
          userCompany: {rules: [
          ]},
          serialNumber: {rules: [
          ]},
          status: {rules: [
          ]},
          remark: {rules: [
          ]},
          idFront: {rules: [
          ]},
          idBack: {rules: [
          ]},
          idHandheld: {rules: [
          ]},
        },
        url: {
          add: "/realname/realNameSystem/add",
          edit: "/realname/realNameSystem/edit",
        },
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
        this.queryDetailsBy(record);
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'iccid','msisdn','name','idCardNumber','mobile','userId','userCompany','createTime','updateTime','serialNumber'))
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
            formData.idFront='';
            formData.idBack='';
            formData.idHandheld='';
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
        this.form.setFieldsValue(pick(row,'iccid','msisdn','name','idCardNumber','mobile','userId','userCompany','createTime','updateTime','serialNumber'))
      },
      queryDetailsBy(record){
        var that = this;
        queryDetails({id: record.id}).then((res)=>{
          if(res.success){
            that.dataSource = [];
            this.$nextTick(() => {
              this.form.setFieldsValue(pick(this.model,'iccid','msisdn','name','idCardNumber','mobile','userId','userCompany','createTime','updateTime','serialNumber'))
            });
            this.dataSource.push({
              key:0,
              fileDetails:[
                {
                  imgUrl:res.result.idFront
                }
              ]
            },{
              key:1,
              fileDetails:[
                {
                  imgUrl:res.result.idBack
                }
              ]
            },{
              key:2,
              fileDetails:[
                {
                  imgUrl:res.result.idHandheld
                }
              ]
            })
          }
        });
      },
      
    }
  }
</script>