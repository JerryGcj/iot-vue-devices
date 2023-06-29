<template>
  <a-modal
    :title="title"
    :width="700"
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


        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="速率">
          <a-select v-decorator="[ 'speedValue', validatorRules.speedValue]" placeholder="请选择">
            <a-select-option value="31">不限制</a-select-option>
            <a-select-option value="10">1Kbps</a-select-option>
            <a-select-option value="32">64Kbps</a-select-option>
            <a-select-option value="33">256Kbps</a-select-option>
            <a-select-option value="11">512Kbps</a-select-option>
            <a-select-option value="12">1Mbps</a-select-option>
            <a-select-option value="13">3Mbps</a-select-option>
            <a-select-option value="14">5Mbps</a-select-option>
            <a-select-option value="15">7Mbps</a-select-option>
            <a-select-option value="16">10Mbps</a-select-option>
            <a-select-option value="17">20Mbps</a-select-option>
            <a-select-option value="18">30Mbps</a-select-option>
            <a-select-option value="19">40Mbps</a-select-option>
            <a-select-option value="20">50Mbps</a-select-option>
            <a-select-option value="21">60Mbps</a-select-option>
            <a-select-option value="22">70Mbps</a-select-option>
            <a-select-option value="23">80Mbps</a-select-option>
            <a-select-option value="24">90Mbps</a-select-option>
            <a-select-option value="25">100Mbps</a-select-option>
            <a-select-option value="26">110Mbps</a-select-option>
            <a-select-option value="27">120Mbps</a-select-option>
            <a-select-option value="28">130Mbps</a-select-option>
            <a-select-option value="29">140Mbps</a-select-option>
            <a-select-option value="30">150Mbps</a-select-option>
          </a-select>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import {queryLowerAgent,stateUpdate } from '@/api/api'

  export default {
    name: "CardInformationSpeedLimit",
    data () {
      return {
        title:"操作",
        visible: false,
        model: {},
        userData:[],
        idsDate:[],
        selectedRowKeys: [],
        cardNumber:'',
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

          speedValue:{
            rules: [{
              required: true, message: '请选择速率!'
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
      add (record,record2) {
        this.selectedRowKeys = record;
        this.idsDate = record2;
        if(this.selectedRowKeys == ''){
          this.cardNumber = this.idsDate.length;
        }else{
          this.cardNumber = this.selectedRowKeys.length;
        }
        this.edit({});
      },
      edit (record) {
        // this.queryUserAgent();
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model))
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
          that.confirmLoading = true;
          var ids = "";
          if(this.selectedRowKeys == ''){
            for (var a = 0; a < this.idsDate.length; a++) {
              ids += this.idsDate[a] + ",";
            }
          }else{
            for (var a = 0; a < this.selectedRowKeys.length; a++) {
              ids += this.selectedRowKeys[a] + ",";
            }
          }
          const formData = new FormData();
          //卡号id
          formData.append('ids',ids);
          //激活后是否生效
          formData.append('speedValue',values.speedValue);

          let httpurl = '/telecomcardinformation/telecomCardInformation/setSpeedValue';
          let method = 'post';

          httpAction(httpurl,formData,method).then((res)=>{
            if(res.success){
              that.$message.success(res.message);
              that.$emit('ok');
              that.$emit('hello');
              this.visible=false;
          }else{
            that.$emit('hello');
            that.$message.warning(res.message);
          }
        }).finally(() => {
            that.confirmLoading = false;
            that.close();
        })
      })
      },
      handleCancel () {
        this.close()
      }

    }
  }
</script>

<style lang="less" scoped>

</style>