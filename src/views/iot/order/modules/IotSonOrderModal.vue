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
        <a-card :bordered="false">
          <detail-list>
            <detail-list-item term="子订单号">{{model.sonOrderId}}</detail-list-item>
            <detail-list-item term="订单号">{{model.orderId}}</detail-list-item>
            <detail-list-item term="MSISDN">{{model.msisdn}}</detail-list-item>
            <detail-list-item term="ICCID">{{model.iccid}}</detail-list-item>
            <detail-list-item term="订单状态">{{model.status}}</detail-list-item>
            <detail-list-item term="企业名称">{{model.userName}}</detail-list-item>
            <detail-list-item term="套餐编号">{{model.setmealId}}</detail-list-item>
            <detail-list-item term="套餐名称">{{model.setmealName}}</detail-list-item>
            <detail-list-item term="生效方式">{{model.sffectiveway}}</detail-list-item>
            <detail-list-item term="创建人">{{model.createUser}}</detail-list-item>
            <detail-list-item term="套餐价格(元)">{{model.sellPrice}}</detail-list-item>
            <detail-list-item term="创建时间">{{model.createTime}}</detail-list-item>
            <detail-list-item term="购买数量">{{model.buyNum}}</detail-list-item>
            <detail-list-item term="充值流水号">{{model.payOrder}}</detail-list-item>
            <detail-list-item term="充值摘要">{{model.msg}}</detail-list-item>
            <detail-list-item term="备注">{{model.orderId}}</detail-list-item>
          </detail-list>
        </a-card>
      <!--<a-form :form="form">

        <a-form-item label="运营商" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input disabled="" v-decorator="[ 'operatorId', validatorRules.operatorId]" placeholder="请输入运营商"></a-input>
        </a-form-item>
        <a-form-item label="客户" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'userNameId', validatorRules.userNameId]" placeholder="请输入客户"></a-input>
        </a-form-item>
        <a-form-item label="合伙人" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'partnerId', validatorRules.partnerId]" placeholder="请输入合伙人"></a-input>
        </a-form-item>
        <a-form-item label="订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'orderId', validatorRules.orderId]" placeholder="请输入订单号"></a-input>
        </a-form-item>
        <a-form-item label="子订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'sonOrderId', validatorRules.sonOrderId]" placeholder="请输入子订单号"></a-input>
        </a-form-item>
        <a-form-item label="ICCID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number  disabled="" v-decorator="[ 'iccid', validatorRules.iccid]" placeholder="请输入ICCID" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="企业" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'enterprise', validatorRules.enterprise]" placeholder="请输入企业"></a-input>
        </a-form-item>
        <a-form-item label="套餐编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'setmealId', validatorRules.setmealId]" placeholder="请输入套餐编号"></a-input>
        </a-form-item>
        <a-form-item label="套餐" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'setmealName', validatorRules.setmealName]" placeholder="请输入套餐"></a-input>
        </a-form-item>
        <a-form-item label="交易金额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number  disabled="" v-decorator="[ 'sellPrice', validatorRules.sellPrice]" placeholder="请输入交易金额" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="购买数量" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number  disabled="" v-decorator="[ 'buyNum', validatorRules.buyNum]" placeholder="请输入购买数量" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number  disabled="" v-decorator="[ 'status', validatorRules.status]" placeholder="请输入状态" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="生效方式" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number  disabled="" v-decorator="[ 'sffectiveway', validatorRules.sffectiveway]" placeholder="请输入生效方式" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="充值摘要" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'msg', validatorRules.msg]" placeholder="请输入充值摘要"></a-input>
        </a-form-item>
        <a-form-item label="创建时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date  disabled="" placeholder="请选择创建时间" v-decorator="[ 'createTime', validatorRules.createTime]" :trigger-change="true" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="创建者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'createUser', validatorRules.createUser]" placeholder="请输入创建者"></a-input>
        </a-form-item>
        <a-form-item label="创建IP" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'createIp', validatorRules.createIp]" placeholder="请输入创建IP"></a-input>
        </a-form-item>
        <a-form-item label="更新日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date  disabled="" placeholder="请选择更新日期" v-decorator="[ 'updateDate', validatorRules.updateDate]" :trigger-change="true" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="更新者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'updateUser', validatorRules.updateUser]" placeholder="请输入更新者"></a-input>
        </a-form-item>
        <a-form-item label="更新IP" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  disabled="" v-decorator="[ 'updateIp', validatorRules.updateIp]" placeholder="请输入更新IP"></a-input>
        </a-form-item>

      </a-form>-->
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JDate from '@/components/jeecg/JDate'
  const DetailListItem = DetailList.Item
  import DetailList from '@/components/tools/DetailList'
  export default {
    name: "IotSonOrderModal",
    components: { 
      JDate,DetailListItem,DetailList
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
          operatorId: {rules: [
          ]},
          userNameId: {rules: [
          ]},
          partnerId: {rules: [
          ]},
          orderId: {rules: [
          ]},
          sonOrderId: {rules: [
          ]},
          iccid: {rules: [
          ]},
          enterprise: {rules: [
          ]},
          setmealId: {rules: [
          ]},
          setmealName: {rules: [
          ]},
          sellPrice: {rules: [
          ]},
          buyNum: {rules: [
          ]},
          status: {rules: [
          ]},
          sffectiveway: {rules: [
          ]},
          msg: {rules: [
          ]},
          createTime: {rules: [
          ]},
          createUser: {rules: [
          ]},
          createIp: {rules: [
          ]},
          updateDate: {rules: [
          ]},
          updateUser: {rules: [
          ]},
          updateIp: {rules: [
          ]},
        },
        url: {
          add: "/iotsonorder/iotSonOrder/add",
          edit: "/iotsonorder/iotSonOrder/edit",
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
        if(this.model.status=='0'){
          this.model.status="创建";
        }else if(this.model.status=="1"){
          this.model.status="处理中";
        }else if(this.model.status=="2"){
          this.model.status="成功";
        }else if(this.model.status=="3"){
          this.model.status="失败";
        }else if(this.model.status=="4"){
          this.model.status="未确认";
        }else if(this.model.status=="5"){
          this.model.status="下个月续费";
        }else if(this.model.status=="6"){
          this.model.status="已提交";
        }else if(this.model.status=="7"){
          this.model.status="待回调";
        }
        if(this.model.sffectiveway=='0'){
          this.model.status="立刻生效";
        }else if(this.model.sffectiveway=="1"){
          this.model.status="下月生效";
        }else if(this.model.sffectiveway=="2"){
          this.model.status="到期生效";
        }
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'operatorId','userNameId','partnerId','orderId','sonOrderId','iccid','enterprise','setmealId','setmealName','sellPrice','buyNum','status','sffectiveway','msg','createTime','createUser','createIp','updateDate','updateUser','updateIp'))
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
        this.form.setFieldsValue(pick(row,'operatorId','userNameId','partnerId','orderId','sonOrderId','iccid','enterprise','setmealId','setmealName','sellPrice','buyNum','status','sffectiveway','msg','createTime','createUser','createIp','updateDate','updateUser','updateIp'))
      },

      
    }
  }
</script>