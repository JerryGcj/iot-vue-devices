<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <!--<a-form-item-->
          <!--:labelCol="labelCol"-->
          <!--:wrapperCol="wrapperCol"-->
          <!--label="卡数量">-->
          <!--<a-input disabled v-model="cardNumber" />-->
        <!--</a-form-item>-->
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="代理商">
          <a-select v-decorator="[ 'agentId', validatorRules.agentId]"
                    placeholder="代理商">
            <a-select-option v-for="d in userData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
          </a-select>
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="分配方式">
          <a-select placeholder="-请选择-" v-model="importType" v-decorator="[ 'importType', validatorRules.importType]">
            <a-select-option value="1">卡号区间</a-select-option>
            <a-select-option value="0">文本框</a-select-option>
          </a-select>
        </a-form-item>

        <a-form-item label="开始卡号" :labelCol="labelCol" :wrapperCol="wrapperCol" v-if="importType === '1'">
          <a-input placeholder="开始卡号" v-decorator="[ 'iccidStart', validatorRules.iccidStart]" />
        </a-form-item>

        <a-form-item label="结束卡号" :labelCol="labelCol" :wrapperCol="wrapperCol" v-if="importType === '1'">
          <a-input placeholder="结束卡号" v-decorator="[ 'iccidEnd', validatorRules.iccidEnd]" />
        </a-form-item>

        <a-form-item label="请输入卡号" :labelCol="labelCol" :wrapperCol="wrapperCol" v-if="importType === '0'">
          <a-textarea placeholder="请输入卡号，每行一个" rows="12" v-decorator="[ 'iccids', validatorRules.iccids]" />
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction,getAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import {queryLowerAgent,cardDistribution } from '@/api/api'
  import ATextarea from "ant-design-vue/es/input/TextArea";

  export default {
    name: "CardInformationDistribution",
    components: {ATextarea},
    data () {
      return {
        title:"操作",
        visible: false,
        importType:'',
        model: {},
        userData:[],
        idsDate:[],
        cardNumber:'',
        selectedRowKeys: [],
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

          agentId:{
            rules: [{
              required: true, message: '请选择下级代理!'
            }]
          },
          importType:{
            rules: [{
              required: true, message: '请选择分配方式!'
            }],},
            iccids:{
              rules: [{
                required: true, message: '请输入卡号!'
              }]
            },
            iccidStart:{
              rules: [{
                required: true, message: '请输入开始卡号!'
              }]
            },
            iccidEnd:{
              rules: [{
                required: true, message: '请输入结束卡号'
              }]
            },
        },
        url: {
          add: "/telecomcardinformation/telecomCardInformation/add",
          edit: "/telecomcardinformation/telecomCardInformation/edit",
        },
      }
    },
    created () {
    },
    methods: {
      add (record) {
        this.selectedRowKeys = record;
        this.cardNumber = this.selectedRowKeys.length;
        /*if(this.selectedRowKeys == ''){
          this.cardNumber = this.idsDate.length;
        }else{
          this.cardNumber = this.selectedRowKeys.length;
        }*/
        this.edit({});
      },
      edit (record) {
        this.queryUserAgent();
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'iccid','msisdn','imsi','imei','deviceOnOffState','cardState','cardOnlineStatus','wirelessAccessType','ipAddress','apn','province','city','areaCode','automaticStopResetMachine','cardSynchronization','trafficUse','packageId','packageName','trafficUseMonth','voiceUseMonth','smsUseMonth','balance','operatorType','importBatch','distributionBatch','customPackageUse','operatorId','operatorName','isTrafficCard','upstreamFlowPoolNumber','agentStopMachine','terminalBlacklist','systemFlowPoolId','isRealNameAuthentication','realNameAuditMethod','platformRealNameAuthentication','upstreamRealNameAuthentication','priority','thirtyDayCard','allocationState','customerId','customerName','isArrearage','gprsState','smsState','isFlowPool','note','createUser','createIp','updateUser','updateIp'))
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
          if(!err){
            that.confirmLoading = true;
            const formData = new FormData();
            if(values.importType == '1'){
              //卡号id
              let start = values.iccidStart;
              let end = values.iccidEnd;
              formData.append('importType',values.importType);
              formData.append('iccidStart',start);
              formData.append('iccidEnd',end);
              //激活后是否生效
              formData.append('customerId',values.agentId);
              if(!start || !end ){
                that.$message.warning('请输入开始或结束卡号！');
                return;
              }
            }
            if(values.importType == '0'){
              formData.append('importType',values.importType);
              formData.append('iccids',values.iccids);
              //激活后是否生效
              formData.append('customerId',values.agentId);
            }

            let httpurl = '/telecomcardinformation/telecomCardInformation/cardDistribution';
            let method = 'post';

            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
                this.visible=false
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.$emit('hello');
              that.confirmLoading = false;
              that.close();
            })
          }
        })
      },
      handleCancel () {
        this.close()
      },

      queryUserAgent(){
        getAction('/sys/user/queryStore').then((res)=>{
          if(res.success){
            this.userData = [];
            let treeList = res.result
            for(let a=0;a<treeList.length;a++){
              let temp = treeList[a];
              this.userData.push({
                value:temp.id,
                text:temp.userCompany
              })
            }
          }else{
            this.$message.warning(res.message);
          }
        }).finally(() => {

        })




      },


    }
  }
</script>

<style lang="less" scoped>

</style>