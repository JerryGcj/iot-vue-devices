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

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="卡数量">
          <a-input disabled v-model="cardNumber" />
        </a-form-item>


<!--        <a-form-item-->
<!--          :labelCol="labelCol"-->
<!--          :wrapperCol="wrapperCol"-->
<!--          label="下级代理">-->
<!--          <a-select v-decorator="[ 'agentId', validatorRules.agentId]"-->
<!--                    placeholder="下级代理">-->
<!--            <a-select-option v-for="d in userData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>-->
<!--          </a-select>-->
<!--        </a-form-item>-->
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="代理商">
          <a-select
            allowClear="true"
            v-model="selectValue"
            show-search
            style="width: 100%"
            placeholder="请选择"
            :options="userData"
            :filterOption="likeQuery"
            :autoClearSearchValue="true"
            :maxTagTextLength=3
            :maxTagCount=3
          ></a-select>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction,getAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import {queryLowerAgent,cardDistribution } from '@/api/api'

  export default {
    name: "CardInformationDistribution",
    data () {
      return {
        title:"操作",
        visible: false,
        model: {},
        selectValue:[],
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

          // agentId:{
          //   rules: [{
          //     required: true, message: '请选择下级代理!'
          //   }]
          // }

        },
        url: {
          add: "/cardinformation/cardInformation/add",
          edit: "/cardinformation/cardInformation/edit",
        },
      }
    },
    created () {
    },
    methods: {
      likeQuery(input, option) {
        return ( option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0 );
      },
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
            var ids = "";
            for (var a = 0; a < this.selectedRowKeys.length; a++) {
              ids += this.selectedRowKeys[a] + ",";
            }
            const formData = new FormData();
            //卡号id
            formData.append('ids',ids);
            //激活后是否生效
            formData.append('customerId',this.selectValue);
            let httpurl = '/cardinformation/cardInformation/cardDistribution';
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

      queryUserAgent(selectValue){
        var that = this;
        let params={selectValue:selectValue};
        queryLowerAgent(params).then((res)=>{
          if(res.success){
            that.userData = [];
            let treeList = res.result
            for(let a=0;a<treeList.length;a++){
              let temp = treeList[a];
              this.userData.push({
                value:temp.id,
                label:temp.userCompany
              })
            }
          }
        });

      },


    }
  }
</script>

<style lang="less" scoped>

</style>