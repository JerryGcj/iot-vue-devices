<template>
  <a-modal
    :title="title"
    :width="width"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <!--<a-form-item label="外部订单线索id" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
          <!--<a-input v-decorator="[ 'clueId', validatorRules.clueId]" placeholder="请输入外部订单线索id"></a-input>-->
        <!--</a-form-item>-->
        <a-form-item label="客户姓名" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusName', validatorRules.cusName]" placeholder="请输入客户姓名"></a-input>
        </a-form-item>
        <a-form-item label="客户手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusPhone', validatorRules.cusPhone]" placeholder="请输入客户手机号"></a-input>
        </a-form-item>
        <a-form-item label="身份证号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusIdno', validatorRules.cusIdno]" placeholder="请输入身份证号"></a-input>
        </a-form-item>
        <a-form-item label="省份" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'province', validatorRules.province]" placeholder="请输入省份"></a-input>
        </a-form-item>
        <a-form-item label="城市" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'city', validatorRules.city]" placeholder="请输入城市"></a-input>
        </a-form-item>
        <a-form-item label="区(县)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'district', validatorRules.district]" placeholder="请输入区(县)"></a-input>
        </a-form-item>
        <a-form-item label="详细地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea :rows="rows" v-decorator="[ 'detailAddr', validatorRules.detailAddr]" placeholder="请输入详细地址"></a-textarea>
        </a-form-item>
        <a-form-item label="订单状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'orderStatus', validatorRules.orderStatus]" placeholder="请选择">
            <a-select-option v-for="d in dictOptions" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="上游单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderNum', validatorRules.orderNum]" placeholder="请输入上游单号"></a-input>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import ATextarea from "ant-design-vue/es/input/TextArea";
  import JDate from '@/components/jeecg/JDate'
  import {ajaxGetDictItems} from '@/api/api'

  export default {
    name: "ElectronChannelOrderModal",
    components: {
      JDate,
      ATextarea
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
        model: {},
        dictOptions: [],
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        rows:3,
        confirmLoading: false,
        validatorRules: {
          clueId: {rules: [
          ]},
          cusName: {
            rules: [
              { required: true, message: '请填写客户姓名!'}
            ]
          },
          cusPhone: {
            rules: [
              { required: true, message: '请填写客户手机号!'}
            ]
          },
          cusIdno: {rules: [
              { required: true, message: '请填写身份证号!'}
          ]},
          detailAddr: {
            rules: [
              { required: true, message: '请填写详细地址!'}
            ]
          },
          province: {rules: [
              { required: true, message: '请填写省份!'}
          ]},
          city: {rules: [
              { required: true, message: '请填写城市!'}
          ]},
          district: {rules: [
              { required: true, message: '请填写区(县)!'}
          ]},
          iccid: {rules: []},
          agentId: {rules: []},
          orderStatus: {rules: []},
          createTime: {rules: []},
        },
        url: {
          add: "/electronregular/electronChannelOrderRegular/add",
          edit: "/electronregular/electronChannelOrderRegular/edit",
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
        this.initDictData();
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'cusName','cusPhone','cusIdno','detailAddr','province','city','district','orderStatus','createTime','updateBy','updateTime','orderNum'))
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
            if (formData.orderSource !== "5") {
              formData.cancelMsg = ""
            }
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
      initDictData() {
        //根据字典Code, 初始化字典数组
        ajaxGetDictItems('electron_waist_order_status', null).then((res) => {
          if (res.success) {
            //console.log(res.result);
            this.dictOptions = res.result;
          }
        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'clueId','cusName','cusPhone','cusIdno','detailAddr','province','city','district','orderStatus','importBatch','createBy','createTime','updateBy','updateTime','orderNum'))
      },


    }
  }
</script>