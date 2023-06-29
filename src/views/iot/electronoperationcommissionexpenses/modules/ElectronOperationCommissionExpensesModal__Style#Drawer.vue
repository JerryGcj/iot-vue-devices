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

        <a-form-item label="运营商id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'operatorId', validatorRules.operatorId]" placeholder="请输入运营商id"></a-input>
        </a-form-item>
        <a-form-item label="支出账号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusName', validatorRules.cusName]" placeholder="请输入支出账号"></a-input>
        </a-form-item>
        <a-form-item label="激活月份（入网月）" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'activateMonth', validatorRules.activateMonth]" placeholder="请输入激活月份（入网月）"></a-input>
        </a-form-item>
        <a-form-item label="接入号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'accessNumber', validatorRules.accessNumber]" placeholder="请输入接入号"></a-input>
        </a-form-item>
        <a-form-item label="1:渠道  2:代理" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusType', validatorRules.cusType]" placeholder="请输入1:渠道  2:代理"></a-input>
        </a-form-item>
        <a-form-item label="0.X：抽成比  100：一次性结佣" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'commissionPolicy', validatorRules.commissionPolicy]" placeholder="请输入0.X：抽成比  100：一次性结佣" style="width: 100%"/>
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
    name: "ElectronOperationCommissionExpensesModal",
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
          operatorId: {rules: [
          ]},
          cusName: {rules: [
          ]},
          activateMonth: {rules: [
          ]},
          accessNumber: {rules: [
          ]},
          cusType: {rules: [
          ]},
          commissionPolicy: {rules: [
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
          add: "/electronoperationcommissionexpenses/electronOperationCommissionExpenses/add",
          edit: "/electronoperationcommissionexpenses/electronOperationCommissionExpenses/edit",
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
          this.form.setFieldsValue(pick(this.model,'operatorId','cusName','activateMonth','accessNumber','cusType','commissionPolicy','createBy','createTime','updateBy','updateTime'))
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
        this.form.setFieldsValue(pick(row,'operatorId','cusName','activateMonth','accessNumber','cusType','commissionPolicy','createBy','createTime','updateBy','updateTime'))
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