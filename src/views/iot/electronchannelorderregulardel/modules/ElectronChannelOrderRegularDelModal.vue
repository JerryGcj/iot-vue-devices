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

        <a-form-item label="用户id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusId', validatorRules.cusId]" placeholder="请输入用户id"></a-input>
        </a-form-item>
        <a-form-item label="客户姓名" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusName', validatorRules.cusName]" placeholder="请输入客户姓名"></a-input>
        </a-form-item>
        <a-form-item label="客户手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusPhone', validatorRules.cusPhone]" placeholder="请输入客户手机号"></a-input>
        </a-form-item>
        <a-form-item label="身份证号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusIdno', validatorRules.cusIdno]" placeholder="请输入身份证号"></a-input>
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
          <a-input v-decorator="[ 'detailAddr', validatorRules.detailAddr]" placeholder="请输入详细地址"></a-input>
        </a-form-item>
        <a-form-item label="代理商id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'agentId', validatorRules.agentId]" placeholder="请输入代理商id"></a-input>
        </a-form-item>
        <a-form-item label="提交电信平台id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'ourId', validatorRules.ourId]" placeholder="请输入提交电信平台id"></a-input>
        </a-form-item>
        <a-form-item label="外部订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'outTradeNo', validatorRules.outTradeNo]" placeholder="请输入外部订单号"></a-input>
        </a-form-item>
        <a-form-item label="渠道名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'channelName', validatorRules.channelName]" placeholder="请输入渠道名称"></a-input>
        </a-form-item>
        <a-form-item label="订单状态(1:初始化，2提交成功，3提交失败,4:已发货未签收,5:订单作废,6:已激活，7:已签收未激活)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderStatus', validatorRules.orderStatus]" placeholder="请输入订单状态(1:初始化，2提交成功，3提交失败,4:已发货未签收,5:订单作废,6:已激活，7:已签收未激活)"></a-input>
        </a-form-item>
        <a-form-item label="提单日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择提单日期" v-decorator="[ 'commitTime', validatorRules.commitTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="快递单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'expressNo', validatorRules.expressNo]" placeholder="请输入快递单号"></a-input>
        </a-form-item>
        <a-form-item label="物流公司" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'expressCompany', validatorRules.expressCompany]" placeholder="请输入物流公司"></a-input>
        </a-form-item>
        <a-form-item label="快递当前状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'expressState', validatorRules.expressState]" placeholder="请输入快递当前状态"></a-input>
        </a-form-item>
        <a-form-item label="物流信息详情" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'logisticsDetails', validatorRules.logisticsDetails]" placeholder="请输入物流信息详情"></a-input>
        </a-form-item>
        <a-form-item label="快递100接口返回的错误信息" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'errorMsg', validatorRules.errorMsg]" placeholder="请输入快递100接口返回的错误信息"></a-input>
        </a-form-item>
        <a-form-item label="设备号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'iccid', validatorRules.iccid]" placeholder="请输入设备号"></a-input>
        </a-form-item>
        <a-form-item label="接入号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'accessNumber', validatorRules.accessNumber]" placeholder="请输入接入号"></a-input>
        </a-form-item>
        <a-form-item label="入网时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'networkAccessTime', validatorRules.networkAccessTime]" placeholder="请输入入网时间"></a-input>
        </a-form-item>
        <a-form-item label="订单作废的原因" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cancelMsg', validatorRules.cancelMsg]" placeholder="请输入订单作废的原因"></a-input>
        </a-form-item>
        <a-form-item label="订单来源（1：无语音；2：有语音）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'fromType', validatorRules.fromType]" placeholder="请输入订单来源（1：无语音；2：有语音）" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="短信通知状态(1:已发货未签收通知，2:已签收未激活通知，3:作废通知)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'smsStatus', validatorRules.smsStatus]" placeholder="请输入短信通知状态(1:已发货未签收通知，2:已签收未激活通知，3:作废通知)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="上游返回订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderNum', validatorRules.orderNum]" placeholder="请输入上游返回订单号"></a-input>
        </a-form-item>
        <a-form-item label="激活时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择激活时间" v-decorator="[ 'activationDate', validatorRules.activationDate]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="省编码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'provinceCode', validatorRules.provinceCode]" placeholder="请输入省编码"></a-input>
        </a-form-item>
        <a-form-item label="市编码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cityCode', validatorRules.cityCode]" placeholder="请输入市编码"></a-input>
        </a-form-item>
        <a-form-item label="是否可提交（1:是，0否）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'canSubmit', validatorRules.canSubmit]" placeholder="请输入是否可提交（1:是，0否）"></a-input>
        </a-form-item>
        <a-form-item label="订单来源 (1:落地页,2:订单导入)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderSource', validatorRules.orderSource]" placeholder="请输入订单来源 (1:落地页,2:订单导入)"></a-input>
        </a-form-item>
        <a-form-item label="创建者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'createBy', validatorRules.createBy]" placeholder="请输入创建者"></a-input>
        </a-form-item>
        <a-form-item label="创建时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择创建时间" v-decorator="[ 'createTime', validatorRules.createTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="更新者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'updateBy', validatorRules.updateBy]" placeholder="请输入更新者"></a-input>
        </a-form-item>
        <a-form-item label="更新时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择更新时间" v-decorator="[ 'updateTime', validatorRules.updateTime]" :trigger-change="true" style="width: 100%"/>
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
    name: "ElectronChannelOrderRegularDelModal",
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
          cusId: {rules: [
          ]},
          cusName: {rules: [
          ]},
          cusPhone: {rules: [
          ]},
          cusIdno: {rules: [
          ]},
          province: {rules: [
          ]},
          city: {rules: [
          ]},
          district: {rules: [
          ]},
          detailAddr: {rules: [
          ]},
          agentId: {rules: [
          ]},
          ourId: {rules: [
          ]},
          outTradeNo: {rules: [
          ]},
          channelName: {rules: [
          ]},
          orderStatus: {rules: [
          ]},
          commitTime: {rules: [
          ]},
          expressNo: {rules: [
          ]},
          expressCompany: {rules: [
          ]},
          expressState: {rules: [
          ]},
          logisticsDetails: {rules: [
          ]},
          errorMsg: {rules: [
          ]},
          iccid: {rules: [
          ]},
          accessNumber: {rules: [
          ]},
          networkAccessTime: {rules: [
          ]},
          cancelMsg: {rules: [
          ]},
          fromType: {rules: [
          ]},
          smsStatus: {rules: [
          ]},
          orderNum: {rules: [
          ]},
          activationDate: {rules: [
          ]},
          provinceCode: {rules: [
          ]},
          cityCode: {rules: [
          ]},
          canSubmit: {rules: [
          ]},
          orderSource: {rules: [
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
          add: "/electronchannelorderregulardel/electronChannelOrderRegularDel/add",
          edit: "/electronchannelorderregulardel/electronChannelOrderRegularDel/edit",
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
          this.form.setFieldsValue(pick(this.model,'cusId','cusName','cusPhone','cusIdno','province','city','district','detailAddr','agentId','ourId','outTradeNo','channelName','orderStatus','commitTime','expressNo','expressCompany','expressState','logisticsDetails','errorMsg','iccid','accessNumber','networkAccessTime','cancelMsg','fromType','smsStatus','orderNum','activationDate','provinceCode','cityCode','canSubmit','orderSource','createBy','createTime','updateBy','updateTime'))
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
        this.form.setFieldsValue(pick(row,'cusId','cusName','cusPhone','cusIdno','province','city','district','detailAddr','agentId','ourId','outTradeNo','channelName','orderStatus','commitTime','expressNo','expressCompany','expressState','logisticsDetails','errorMsg','iccid','accessNumber','networkAccessTime','cancelMsg','fromType','smsStatus','orderNum','activationDate','provinceCode','cityCode','canSubmit','orderSource','createBy','createTime','updateBy','updateTime'))
      },

      
    }
  }
</script>