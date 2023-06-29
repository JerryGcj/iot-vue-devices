<template>
  <a-drawer
      :title="title"
      :width="800"
      placement="right"
      :closable="false"
      @close="close"
      :visible="visible"
  >

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
      
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="用户名">
          <a-input placeholder="请输入用户名" v-decorator="['userName', validatorRules.userName ]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="密码">
          <a-input placeholder="请输入密码" v-decorator="['password', validatorRules.password ]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上级代理ID">
          <a-input-number v-decorator="[ 'higherAgentId', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上级代理用户名">
          <a-input placeholder="请输入上级代理用户名" v-decorator="['higherAgentName', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="状态 0:可用 1:禁用">
          <a-input placeholder="请输入状态 0:可用 1:禁用" v-decorator="['state', validatorRules.state ]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="预存金额">
          <a-input-number v-decorator="[ 'amountDeposited', validatorRules.amountDeposited ]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="公司名称">
          <a-input placeholder="请输入公司名称" v-decorator="['userCompany', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="联系人">
          <a-input placeholder="请输入联系人" v-decorator="['theContact', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="联系电话">
          <a-input placeholder="请输入联系电话" v-decorator="['userPhone', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="是否可以开下级代理 0:是 1:否">
          <a-input placeholder="请输入是否可以开下级代理 0:是 1:否" v-decorator="['openAgent', validatorRules.openAgent ]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="0:平台返佣金 1：全额代理返佣 2：上级代理返佣">
          <a-input placeholder="请输入0:平台返佣金 1：全额代理返佣 2：上级代理返佣" v-decorator="['commissionType', validatorRules.commissionType ]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="创建者">
          <a-input placeholder="请输入创建者" v-decorator="['createUser', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="创建IP">
          <a-input placeholder="请输入创建IP" v-decorator="['createIp', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="更新日期">
          <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'updateDate', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="更新者">
          <a-input placeholder="请输入更新者" v-decorator="['updateUser', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="更新IP">
          <a-input placeholder="请输入更新IP" v-decorator="['updateIp', {}]" />
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
  import moment from "moment"

  export default {
    name: "AgentModal",
    data () {
      return {
        title:"操作",
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
        form: this.$form.createForm(this),
        validatorRules:{
        userName:{rules: [{ required: true, message: '请输入用户名!' }]},
        password:{rules: [{ required: true, message: '请输入密码!' }]},
        state:{rules: [{ required: true, message: '请输入状态 0:可用 1:禁用!' }]},
        amountDeposited:{rules: [{ required: true, message: '请输入预存金额!' }]},
        openAgent:{rules: [{ required: true, message: '请输入是否可以开下级代理 0:是 1:否!' }]},
        commissionType:{rules: [{ required: true, message: '请输入0:平台返佣金 1：全额代理返佣 2：上级代理返佣!' }]},
        },
        url: {
          add: "/agent/agent/add",
          edit: "/agent/agent/edit",
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
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'userName','password','higherAgentId','higherAgentName','state','amountDeposited','userCompany','theContact','userPhone','openAgent','commissionType','createUser','createIp','updateUser','updateIp'))
		  //时间格式化
          this.form.setFieldsValue({updateDate:this.model.updateDate?moment(this.model.updateDate):null})
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
            //时间格式化
            formData.updateDate = formData.updateDate?formData.updateDate.format('YYYY-MM-DD HH:mm:ss'):null;
            
            console.log(formData)
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