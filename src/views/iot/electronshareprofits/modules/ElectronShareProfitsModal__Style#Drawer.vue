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

        <a-form-item label="接入号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'accessNumber', validatorRules.accessNumber]" placeholder="请输入接入号"></a-input>
        </a-form-item>
        <a-form-item label="cardNumber" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cardNumber', validatorRules.cardNumber]" placeholder="请输入cardNumber"></a-input>
        </a-form-item>
        <a-form-item label="卡类型（0：移动，1：联通，2：电信）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'cardType', validatorRules.cardType]" placeholder="请输入卡类型（0：移动，1：联通，2：电信）" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="createIp" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'createIp', validatorRules.createIp]" placeholder="请输入createIp"></a-input>
        </a-form-item>
        <a-form-item label="创建日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择创建日期" v-decorator="[ 'createTime', validatorRules.createTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="创建者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'createUser', validatorRules.createUser]" placeholder="请输入创建者"></a-input>
        </a-form-item>
        <a-form-item label="上级代理" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'higherAgent', validatorRules.higherAgent]" placeholder="请输入上级代理"></a-input>
        </a-form-item>
        <a-form-item label="上级代理ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'higherAgentId', validatorRules.higherAgentId]" placeholder="请输入上级代理ID"></a-input>
        </a-form-item>
        <a-form-item label="下级代理" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'lowerAgent', validatorRules.lowerAgent]" placeholder="请输入下级代理"></a-input>
        </a-form-item>
        <a-form-item label="下级代理ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'lowerAgentId', validatorRules.lowerAgentId]" placeholder="请输入下级代理ID"></a-input>
        </a-form-item>
        <a-form-item label="运营商类型 1:移动 2:联通 3:电信 4:第三方" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'operatorType', validatorRules.operatorType]" placeholder="请输入运营商类型 1:移动 2:联通 3:电信 4:第三方" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderNo', validatorRules.orderNo]" placeholder="请输入订单号"></a-input>
        </a-form-item>
        <a-form-item label="套餐ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'packageId', validatorRules.packageId]" placeholder="请输入套餐ID"></a-input>
        </a-form-item>
        <a-form-item label="套餐价格(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'packageMoney', validatorRules.packageMoney]" placeholder="请输入套餐价格(元)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="套餐名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'packageName', validatorRules.packageName]" placeholder="请输入套餐名称"></a-input>
        </a-form-item>
        <a-form-item label="支付单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'payOrderNo', validatorRules.payOrderNo]" placeholder="请输入支付单号"></a-input>
        </a-form-item>
        <a-form-item label="购买数量" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'purchaseQuantity', validatorRules.purchaseQuantity]" placeholder="请输入购买数量" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'remark', validatorRules.remark]" placeholder="请输入备注"></a-input>
        </a-form-item>
        <a-form-item label="serialNumber" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'serialNumber', validatorRules.serialNumber]" placeholder="请输入serialNumber"></a-input>
        </a-form-item>
        <a-form-item label="分润金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'shareMoney', validatorRules.shareMoney]" placeholder="请输入分润金额(元)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="分润单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'shareOrderNo', validatorRules.shareOrderNo]" placeholder="请输入分润单号"></a-input>
        </a-form-item>
        <a-form-item label="我方给一级代理的结算状态（0：未分润，1：已分润）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'shareStatus', validatorRules.shareStatus]" placeholder="请输入我方给一级代理的结算状态（0：未分润，1：已分润）"></a-input>
        </a-form-item>
        <a-form-item label="sonOrderNo" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'sonOrderNo', validatorRules.sonOrderNo]" placeholder="请输入sonOrderNo"></a-input>
        </a-form-item>
        <a-form-item label="一级代理给其下代理的结算状态(0：未分润，1：已分润)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'status', validatorRules.status]" placeholder="请输入一级代理给其下代理的结算状态(0：未分润，1：已分润)"></a-input>
        </a-form-item>
        <a-form-item label="我方分润截止日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择我方分润截止日期" v-decorator="[ 'updateDate', validatorRules.updateDate]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="updateIp" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'updateIp', validatorRules.updateIp]" placeholder="请输入updateIp"></a-input>
        </a-form-item>
        <a-form-item label="更新日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择更新日期" v-decorator="[ 'updateTime', validatorRules.updateTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="更新者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'updateUser', validatorRules.updateUser]" placeholder="请输入更新者"></a-input>
        </a-form-item>
        <a-form-item label="打款方式（1：线下打款；2:公众号提现）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'withdrawMethod', validatorRules.withdrawMethod]" placeholder="请输入打款方式（1：线下打款；2:公众号提现）" style="width: 100%"/>
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
    name: "ElectronShareProfitsModal",
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
          accessNumber: {rules: [
          ]},
          cardNumber: {rules: [
          ]},
          cardType: {rules: [
          ]},
          createIp: {rules: [
          ]},
          createTime: {rules: [
          ]},
          createUser: {rules: [
          ]},
          higherAgent: {rules: [
          ]},
          higherAgentId: {rules: [
          ]},
          lowerAgent: {rules: [
          ]},
          lowerAgentId: {rules: [
          ]},
          operatorType: {rules: [
          ]},
          orderNo: {rules: [
          ]},
          packageId: {rules: [
          ]},
          packageMoney: {rules: [
          ]},
          packageName: {rules: [
          ]},
          payOrderNo: {rules: [
          ]},
          purchaseQuantity: {rules: [
          ]},
          remark: {rules: [
          ]},
          serialNumber: {rules: [
          ]},
          shareMoney: {rules: [
          ]},
          shareOrderNo: {rules: [
          ]},
          shareStatus: {rules: [
          ]},
          sonOrderNo: {rules: [
          ]},
          status: {rules: [
          ]},
          updateDate: {rules: [
          ]},
          updateIp: {rules: [
          ]},
          updateTime: {rules: [
          ]},
          updateUser: {rules: [
          ]},
          withdrawMethod: {rules: [
          ]},
        },
        url: {
          add: "/electronshareprofits/electronShareProfits/add",
          edit: "/electronshareprofits/electronShareProfits/edit",
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
          this.form.setFieldsValue(pick(this.model,'accessNumber','cardNumber','cardType','createIp','createTime','createUser','higherAgent','higherAgentId','lowerAgent','lowerAgentId','operatorType','orderNo','packageId','packageMoney','packageName','payOrderNo','purchaseQuantity','remark','serialNumber','shareMoney','shareOrderNo','shareStatus','sonOrderNo','status','updateDate','updateIp','updateTime','updateUser','withdrawMethod'))
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
        this.form.setFieldsValue(pick(row,'accessNumber','cardNumber','cardType','createIp','createTime','createUser','higherAgent','higherAgentId','lowerAgent','lowerAgentId','operatorType','orderNo','packageId','packageMoney','packageName','payOrderNo','purchaseQuantity','remark','serialNumber','shareMoney','shareOrderNo','shareStatus','sonOrderNo','status','updateDate','updateIp','updateTime','updateUser','withdrawMethod'))
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