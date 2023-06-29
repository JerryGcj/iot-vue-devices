<template>
  <a-drawer
    :title="title"
    :width="width"
    placement="right"
    :closable="false"
    @close="close"
    :visible="visible">
  
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
          <a-input v-decorator="[ 'userId', validatorRules.userId]" placeholder="请输入代理商id"></a-input>
        </a-form-item>
        <a-form-item label="userCompany" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'userCompany', validatorRules.userCompany]" placeholder="请输入userCompany"></a-input>
        </a-form-item>
        <a-form-item label="0:未支付  1：支付退出  2：支付异常 3：支付失败 4：支付成功 5：退款失败  6：退款成功" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'payState', validatorRules.payState]" placeholder="请输入0:未支付  1：支付退出  2：支付异常 3：支付失败 4：支付成功 5：退款失败  6：退款成功"></a-input>
        </a-form-item>
        <a-form-item label="订单状态(1:初始化，2提交成功，3提交失败,4:已发货未签收,5:订单作废,6:已激活，7:已签收未激活)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderState', validatorRules.orderState]" placeholder="请输入订单状态(1:初始化，2提交成功，3提交失败,4:已发货未签收,5:订单作废,6:已激活，7:已签收未激活)"></a-input>
        </a-form-item>
        <a-form-item label="remark" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'remark', validatorRules.remark]" placeholder="请输入remark"></a-input>
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
        <a-form-item label="设备号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'iccid', validatorRules.iccid]" placeholder="请输入设备号"></a-input>
        </a-form-item>
        <a-form-item label="接入号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'accessNumber', validatorRules.accessNumber]" placeholder="请输入接入号"></a-input>
        </a-form-item>
        <a-form-item label="微信openid" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'openid', validatorRules.openid]" placeholder="请输入微信openid"></a-input>
        </a-form-item>
        <a-form-item label="微信支付交易流水号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'wxTransactionId', validatorRules.wxTransactionId]" placeholder="请输入微信支付交易流水号"></a-input>
        </a-form-item>
        <a-form-item label="支付金额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'orderPayPrice', validatorRules.orderPayPrice]" placeholder="请输入支付金额" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="订单支付成功时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择订单支付成功时间" v-decorator="[ 'orderPayTime', validatorRules.orderPayTime]" :trigger-change="true" style="width: 100%"/>
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
    <a-button type="primary" @click="handleOk">确定</a-button>
    <a-button type="primary" @click="handleCancel">取消</a-button>
  </a-drawer>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JDate from '@/components/jeecg/JDate'  
  
  export default {
    name: "IotCardOrderModal",
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
          province: {rules: [
          ]},
          city: {rules: [
          ]},
          district: {rules: [
          ]},
          detailAddr: {rules: [
          ]},
          userId: {rules: [
          ]},
          userCompany: {rules: [
          ]},
          payState: {rules: [
          ]},
          orderState: {rules: [
          ]},
          remark: {rules: [
          ]},
          commitTime: {rules: [
          ]},
          expressNo: {rules: [
          ]},
          expressCompany: {rules: [
          ]},
          iccid: {rules: [
          ]},
          accessNumber: {rules: [
          ]},
          openid: {rules: [
          ]},
          wxTransactionId: {rules: [
          ]},
          orderPayPrice: {rules: [
          ]},
          orderPayTime: {rules: [
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
          add: "/iotcardorder/iotCardOrder/add",
          edit: "/iotcardorder/iotCardOrder/edit",
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
          this.form.setFieldsValue(pick(this.model,'cusId','cusName','cusPhone','province','city','district','detailAddr','userId','userCompany','payState','orderState','remark','commitTime','expressNo','expressCompany','iccid','accessNumber','openid','wxTransactionId','orderPayPrice','orderPayTime','createBy','createTime','updateBy','updateTime'))
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
        this.form.setFieldsValue(pick(row,'cusId','cusName','cusPhone','province','city','district','detailAddr','userId','userCompany','payState','orderState','remark','commitTime','expressNo','expressCompany','iccid','accessNumber','openid','wxTransactionId','orderPayPrice','orderPayTime','createBy','createTime','updateBy','updateTime'))
      }
      
    }
  }
</script>

<style lang="less" scoped>
/** Button按钮间距 */
  .ant-btn {
    margin-left: 30px;
    margin-bottom: 30px;
    float: right;
  }
</style>