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
          label="运营商类型">
          <a-select placeholder="-请选择-"  @change="queryPackage()" v-model="operatorType">
            <a-select-option value="1">移动</a-select-option>
            <a-select-option value="2">联通</a-select-option>
            <a-select-option value="3">电信</a-select-option>
          </a-select>
<!--          <a-select placeholder="-请选择-"  @change="queryPackage()" v-model="operatorType">-->
<!--            <a-select-option value="0">移动</a-select-option>-->
<!--            <a-select-option value="1">联通</a-select-option>-->
<!--            <a-select-option value="2">电信</a-select-option>-->
<!--          </a-select>-->
        </a-form-item>



        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="套餐名称">
          <a-select v-decorator="[ 'packageId', validatorRules.packageId]"
                    placeholder="套餐名称">
            <a-select-option v-for="d in DiscountPackageData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
          </a-select>
        </a-form-item>


        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="状态">
          <a-radio-group  buttonStyle="solid"   v-decorator="[ 'state', validatorRules.state]">
            <a-radio-button value="0">上架</a-radio-button>
            <a-radio-button value="1">下架</a-radio-button>
          </a-radio-group>
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="销售价格（元）">
          <a-input-number v-decorator="[ 'salesPrice', validatorRules.salesPrice]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="备注">
          <a-input placeholder="请输入备注" v-decorator="['note', {}]" />
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
  import {queryLowerAgent,queryDiscountPackageByOperator } from '@/api/api'
  import JMultiSelectTag from '@/components/dict/JMultiSelectTag'

  export default {
    name: "CustomerSalesDiscountModal",
    components: {JMultiSelectTag},
    data () {
      return {
        title:"操作",
        visible: false,
        model: {},
        userIds: "",
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
        form: this.$form.createForm(this),
        userData:[],
        DiscountPackageData:[],
        operatorType:"",
        validatorRules:{
          packageId:{
            rules: [{
              required: true, message: '请选择套餐!'
            }]
          },
          state:{
            rules: [{
              required: true, message: '请选择状态!'
            }]
          },
          salesPrice:{
            rules: [{
              required: true, message: '请输入销售价格!'
            }]
          },
        },
        url: {
          add: "/customersalesdiscount/customerSalesDiscount/add",
          edit: "/customersalesdiscount/customerSalesDiscount/edit",
        },
      }
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.userIds = record.id;
        this.visible = true;
        /*this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'operatorType','packageId','packageName','agentId','agentName','state','salesPrice','note','createUser','createIp','updateUser','updateIp'))
          //时间格式化
          this.form.setFieldsValue({updateDate:this.model.updateDate?moment(this.model.updateDate):null})
        });*/

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
          let httpurl = this.url.add;
          let method = 'post';
          let formData = Object.assign(this.model, values);
          formData.agentId = this.userIds;
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
      queryUserAgent(){
        var that = this;
        queryLowerAgent().then((res)=>{
          if(res.success){
          that.userData = [];
          let treeList = res.result
          for(let a=0;a<treeList.length;a++){
            let temp = treeList[a];
            this.userData.push({
              value:temp.id,
              text:temp.userCompany
            })
          }
        }
      });

      },

      queryPackage(){

        var that = this;
        queryDiscountPackageByOperator({operatorType: this.operatorType.toString()}).then((res)=>{
          console.log(res)
          if(res.success){
          that.DiscountPackageData = [];
          let treeList = res.result

          for(let a=0;a<treeList.length;a++){
            let temp = treeList[a];
            this.DiscountPackageData.push({
              value:temp.id,
              text:temp.packageName
            })
          }
        }
      });


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