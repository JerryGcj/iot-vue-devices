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

        <a-form-item label="创建日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择创建日期" v-decorator="[ 'createTime', validatorRules.createTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="创建者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'createUser', validatorRules.createUser]" placeholder="请输入创建者"></a-input>
        </a-form-item>
        <a-form-item label="结算标识（0：我方给一级代理结算，1：一级代理给其代理结算）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'flag', validatorRules.flag]" placeholder="请输入结算标识（0：我方给一级代理结算，1：一级代理给其代理结算）" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="已分润金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'hasMoney', validatorRules.hasMoney]" placeholder="请输入已分润金额(元)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="未分润金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'noMoney', validatorRules.noMoney]" placeholder="请输入未分润金额(元)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="运营商类型（1：移动 2：联通 3：电信）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'operatorType', validatorRules.operatorType]" placeholder="请输入运营商类型（1：移动 2：联通 3：电信）" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="分润金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'shareMoney', validatorRules.shareMoney]" placeholder="请输入分润金额(元)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="一级代理给其下代理的结算状态(0：未分润，1：已分润)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'status', validatorRules.status]" placeholder="请输入一级代理给其下代理的结算状态(0：未分润，1：已分润)"></a-input>
        </a-form-item>
        <a-form-item label="分润区间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'updateTime', validatorRules.updateTime]" placeholder="请输入分润区间"></a-input>
        </a-form-item>
        <a-form-item label="更新者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'updateUser', validatorRules.updateUser]" placeholder="请输入更新者"></a-input>
        </a-form-item>
        <a-form-item label="用户ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'userId', validatorRules.userId]" placeholder="请输入用户ID"></a-input>
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
    name: "ElectronShareProfitsHistoryModal",
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
          createTime: {rules: [
          ]},
          createUser: {rules: [
          ]},
          flag: {rules: [
          ]},
          hasMoney: {rules: [
          ]},
          noMoney: {rules: [
          ]},
          operatorType: {rules: [
          ]},
          shareMoney: {rules: [
          ]},
          status: {rules: [
          ]},
          updateTime: {rules: [
          ]},
          updateUser: {rules: [
          ]},
          userId: {rules: [
          ]},
          withdrawMethod: {rules: [
          ]},
        },
        url: {
          add: "/electronshareprofitshistory/electronShareProfitsHistory/add",
          edit: "/electronshareprofitshistory/electronShareProfitsHistory/edit",
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
          this.form.setFieldsValue(pick(this.model,'createTime','createUser','flag','hasMoney','noMoney','operatorType','shareMoney','status','updateTime','updateUser','userId','withdrawMethod'))
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
        this.form.setFieldsValue(pick(row,'createTime','createUser','flag','hasMoney','noMoney','operatorType','shareMoney','status','updateTime','updateUser','userId','withdrawMethod'))
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