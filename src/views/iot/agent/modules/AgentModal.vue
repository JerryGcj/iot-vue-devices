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
          <a-input placeholder="请输入用户名" style="width: 500px" v-decorator="['userName', validatorRules.userName ]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="密码">
          <a-input placeholder="请输入密码" style="width: 500px" v-decorator="['password', validatorRules.password ]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上级代理">
          <a-select style="width:100%"
                    :dropdownStyle="{ maxHeight: '200px', overflow: 'auto' }"
                    v-decorator="[ 'higherAgentId', {}]"
                    placeholder="请选择上级代理"
          >
            <!--<a-select-option value="0">顶级代理</a-select-option>-->
            <a-select-option v-for="d in agentData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
          </a-select>
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="状态">
          <a-select
            v-decorator="[ 'state', validatorRules.state]"
            placeholder="-请选择-"
            :disabled="disableSubmit" style="width: 500px">
            <a-select-option value="0">可用</a-select-option>
            <a-select-option value="1">禁用</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="预存金额">
          <a-input-number style="width: 300px" v-decorator="[ 'amountDeposited', validatorRules.amountDeposited ]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="公司名称">
          <a-input style="width: 500px" placeholder="请输入公司名称" v-decorator="['userCompany', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="联系人">
          <a-input style="width: 500px" placeholder="请输入联系人" v-decorator="['theContact', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="联系电话">
          <a-input style="width: 500px" placeholder="请输入联系电话" v-decorator="['userPhone', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="是否可以开下级代理">
          <a-select
            v-decorator="[ 'openAgent', validatorRules.openAgent]"
            placeholder="-请选择-"
            :disabled="disableSubmit" style="width: 500px">
            <a-select-option value="0">是</a-select-option>
            <a-select-option value="1">否</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="返佣类型">
          <a-select
            v-decorator="[ 'commissionType', validatorRules.commissionType]"
            placeholder="-请选择-"
            :disabled="disableSubmit" style="width: 500px">
            <a-select-option value="0">平台返佣金</a-select-option>
            <a-select-option value="1">全额代理返佣</a-select-option>
            <a-select-option value="2">上级代理返佣</a-select-option>
          </a-select>
        </a-form-item>
      </a-form>
    </a-spin>
    <a-button style="right: 80px" type="primary" @click="handleOk">确定</a-button>
    <a-button  style="right: 80px" type="primary" @click="handleCancel">取消</a-button>
  </a-drawer>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import {queryAgent } from '@/api/api'

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
        agentData:[],

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
        this.loadAgent(record);
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'userName','password','higherAgentId','higherAgentName','state','amountDeposited','userCompany','theContact','userPhone','openAgent','commissionType','createUser','createIp','updateUser','updateIp'))
        //时间格式化
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
      loadAgent(record){

        if(record.id== null){
          record.id = "";
        }
        var that = this;
        queryAgent({id: record.id}).then((res)=>{
          if(res.success){
          that.agentData = [];
          let treeList = res.result
          for(let a=0;a<treeList.length;a++){
            let temp = treeList[a];
            this.agentData.push({
              value:temp.id,
              text:temp.userName
            })
          }
          this.agentData.push({
            value:'0',
            text:'顶级代理'
          })
        }
      });
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