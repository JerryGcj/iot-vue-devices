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
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="下级代理">
          <j-multi-select-tag
            v-decorator="[ 'userId', {}]"
            :options="dictOptions"
            placeholder="请选择下级代理">
          </j-multi-select-tag>
        </a-form-item>
        <a-form-item label="运营商类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag v-decorator="['operatorType', validatorRules.operatorType]" :triggerChange="true"
                             placeholder="请输入运营商类型" dictCode="operator_type"/>
        </a-form-item>
        <a-form-item label="产品名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'name', validatorRules.name]" placeholder="请输入产品名称"></a-input>
        </a-form-item>
        <a-form-item label="充值面额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'money', validatorRules.money]" placeholder="请输入充值面额" style="width: 100%"/>
        </a-form-item>
        <!--<a-form-item label="推广员返佣金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'promoterRebate', validatorRules.promoterRebate]" placeholder="请输入推广员返佣金额" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="店铺返佣金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'storeRebate', validatorRules.storeRebate]" placeholder="请输入店铺返佣金额" style="width: 100%"/>
        </a-form-item>-->
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="赠送方式">
          <a-select placeholder="-请选择-" v-model="givingType" v-decorator="[ 'givingType', validatorRules.givingType]">
            <a-select-option value="0">优惠券</a-select-option>
            <a-select-option value="1">现金余额</a-select-option>
            <a-select-option value="2">套餐体验包</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item v-if="givingType === '0'" label="用户返优惠券金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'couponMoney', validatorRules.couponMoney]" placeholder="请输入用户返优惠券金额" style="width: 100%"/>
        </a-form-item>
        <a-form-item v-if="givingType === '0'" label="用户返优惠券张数" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'couponCount', validatorRules.couponCount]" placeholder="请输入用户返优惠券张数" style="width: 100%"/>
        </a-form-item>
        <a-form-item v-if="givingType === '0'" label="优惠券最小使用金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'couponLimit', validatorRules.couponLimit]" placeholder="请输入优惠券最小使用金额" style="width: 100%"/>
        </a-form-item>
        <a-form-item v-if="givingType === '1'" label="赠送金额(元)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'givingMoney', validatorRules.givingMoney]" placeholder="请输入赠送金额" style="width: 100%"/>
        </a-form-item>
        <a-form-item v-if="givingType === '2'" label="套餐" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag v-decorator="[ 'packageId', validatorRules.packageId]" placeholder="请选择套餐" :triggerChange="true"
              dictCode="iot_standard_cost,package_name,id,free_type='1' order by create_time desc"/>
        </a-form-item>
        <a-form-item label="状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-radio-group  buttonStyle="solid"   v-decorator="[ 'status', validatorRules.status]">
            <a-radio-button value="0">上架</a-radio-button>
            <a-radio-button value="1">下架</a-radio-button>
          </a-radio-group>
        </a-form-item>
        <a-form-item label="是否赠送" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-radio-group  buttonStyle="solid"   v-decorator="[ 'handsel', validatorRules.handsel]">
            <a-radio-button value="0">赠送</a-radio-button>
            <a-radio-button value="1">不赠送</a-radio-button>
          </a-radio-group>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea placeholder="请输入备注" v-decorator="['note', validatorRules.note]" />
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JDate from '@/components/jeecg/JDate'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  import JMultiSelectTag from '@/components/dict/JMultiSelectTag'
  import {queryLowerAgent } from '@/api/api'

  export default {
    name: "IotRechargeProductModal",
    components: { 
      JDate,JDictSelectTag,JMultiSelectTag
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
        confirmLoading: false,
        validatorRules: {
          name: {rules: [{required: true, message: '请输入产品名称!'}
          ]},
          money: {rules: [{required: true, message: '请输入充值面额!'}
          ]},
          givingType: {rules: [{required: true, message: '请选择赠送方式!'}
          ]},
          givingMoney: {rules: [{required: true, message: '请输入赠送金额!'}
          ]},
          couponMoney: {rules: [{required: true, message: '请输入返佣金额!'}
          ]},
          couponCount: {rules: [{required: true, message: '请输入赠送张数!'}
          ]},
          couponLimit: {rules: [{required: true, message: '请输入使用限制'}
          ]},
          status:{
            rules: [{
              required: true, message: '请选择状态!'
            }]
          },
          handsel:{
            rules: [{
              required: true, message: '请选择是否赠送优惠券!'
            }]
          },
          operatorType:{
            rules: [{
              required: true, message: '请选择运营商类型!'
            }]
          },
          packageId:{
            rules: [{
              required: true, message: '请选择套餐!'
            }]
          }
        },
        url: {
          add: "/iotrechargeproduct/iotRechargeProduct/add",
          edit: "/iotrechargeproduct/iotRechargeProduct/edit",
        }
      }
    },
    created () {
      var that = this;
      queryLowerAgent().then((res)=>{
        if(res.success){
          that.dictOptions = [];
          let treeList = res.result
          for(let a=0;a<treeList.length;a++){
            let temp = treeList[a];
            this.dictOptions.push({
              value:temp.id,
              label:temp.userCompany
            })
          }
        }
      });
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.givingType = this.model.givingType;
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'userId','name','money','promoterRebate','storeRebate','status','givingType','givingMoney','couponLimit','couponCount',
            'couponMoney','handsel','operatorType','note','packageId','createTime','createBy','updateTime','updateBy'))
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
        this.form.setFieldsValue(pick(row,'userId','name','money','promoterRebate','storeRebate','status','givingType','givingMoney','note','createTime',
          'couponMoney','handsel','operatorType','packageId','createBy','updateTime','updateBy'))
      },

      
    }
  }
</script>