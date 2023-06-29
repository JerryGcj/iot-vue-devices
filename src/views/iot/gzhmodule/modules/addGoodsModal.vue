<template>
  <a-modal
    title="配置产品"
    :width="600"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleSubmit"
    @cancel="handleCancel"
    cancelText="关闭"
    style="top:20px;"
  >
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="选择产品" :labelCol="labelCol" :wrapperCol="wrapperCol"  v-show="show">
            <a-select
              allowClear="true"
              v-model="operatorValue"
              style="width: 100%"
              placeholder="请选择"
              :options="dictOperatorOptions"
              :filterOption="likeQuery"
              :autoClearSearchValue="false"
              :maxTagTextLength=3
              :maxTagCount=3
            ></a-select>
          </a-form-item>
<!--        <a-form-item label="排序" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
<!--          <a-input-number v-decorator="[ 'sort', validatorRules.sort]" placeholder="请输入排序" style="width: 100%"/>-->
<!--        </a-form-item>-->
        <a-form-item label="排序" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'sort', validatorRules.sort]" placeholder="请输入排序"/>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { postAction,getAction } from '@/api/manage'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  import JCheckbox from '@/components/jeecg/JCheckbox'

  import GzhModuleList from "../GzhModuleList";
  import {httpAction} from "../../../../api/manage";

  export default {
    name: "PasswordModal",
    mixins:[JeecgListMixin],
    components: {
      JCheckbox,
      GzhModuleList
    },
    data () {
      return {
        show: "true",
        id: "",
        props: {
          searchQuery2: {
            type: Function,
            default: null
          }
        },
        appId:"",
        moduleId:"",
        operatorValue: "",
        dictOperatorOptions:[],
        model1: {},
        currentAgentId: '',
        userId: '',
        username:'',
        visible: false,
        confirmLoading: false,
        confirmDirty: false,
        selectedRowKeys1: [],
        selectionRows1: [],
        validatorRules:{
          goodsId:{
              rules: [{
                required: true, message: '请选择产品!'
              }]
            },
          sort:{
            rules: [{
              required: true, message: '请输入序号!'
            }]
          },
        },
        jcheckbox: {
          values: '',
          options: []
        },
        url: {
          list: "/electronchannelagent/electronChannelAgent/list",
          add: "/gzhmodulegoods/gzhModuleGoods/add",
          edit: "/gzhmodulegoods/gzhModuleGoods/edit",
          initOperatorUrl: "/gzhmodule/gzhModule/initOperator",
        },
        model: {},

        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 15 },
        },
        form:this.$form.createForm(this)
      }
    },

    methods: {
      likeQuery(input, option){
        return (option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0)
      },
      initOperator() {
        let params = {appId:this.appId};
        console.log(params)
        getAction(this.url.initOperatorUrl,params).then((res) => {
          if (res.success) {
            this.dictOperatorOptions = res.result
          }
        })
      },
      edit (appId,gzhModuleId) {
        debugger
        if (appId) {
          this.show = 'true'
        } else if (typeof gzhModuleId === 'string') {
          this.id = gzhModuleId;
          this.show = !this.show
        }
        // this.id = gzhModuleId;
        // if (gzhModuleId !== "") {
        //   this.show = !this.show
        // }
        this.visible = true;
        this.appId = appId;
        this.initOperator();
        this.form.validateFields.sort = ""
      },
      close () {
        this.$emit('close');
        this.show = 'false'
        this.visible = false;
        this.disableSubmit = false;
        this.currentAgentId = '';
        this.selectedRowKeys1 = [];
        this.selectionRows1 = [];
        this.operatorValue = ""
        this.form.setFieldsValue({
          sort: ""
        })
      },
      handleSubmit () {
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            debugger
            const that = this;
            let httpurl = '';
            let method = '';
            let param = {}
            debugger
            if(this.show){
              httpurl+=this.url.add;
              method = 'post';
              param = {goodsId:that.operatorValue,moduleId:that.moduleId,sort:values.sort}


            }else{
              httpurl+=this.url.edit;
              method = 'put';
              param = {id:that.id,sort:values.sort};

            }

            that.confirmLoading = true;

            httpAction(httpurl,param,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok')
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            });
          }

        })
      },
      handleCancel () {
        this.close()
      },

      handleConfirmBlur  (e) {
        const value = e.target.value
        this.confirmDirty = this.confirmDirty || !!value
      },
      onSelectChange1(selectedRowKeys, selectionRows) {
        this.rightcolval = 1
        this.selectedRowKeys1 = selectedRowKeys
        this.selectionRows1 = selectionRows
        this.model1 = Object.assign({}, selectionRows[0])
        console.log(this.model1)
        this.currentAgentId = selectedRowKeys[0]
        console.log('gfsgsdggfdbhsd gfsgdf'+this.currentAgentId)
      },
      queryLowerAgent(){
        var that = this;
        getAction('/electronchannelagent/electronChannelAgent/list1',null).then((res)=>{
          if(res.success){
            console.log('sdfsdss===='+JSON.stringify(res))
            that.jcheckbox.options = [];
            let treeList = res.result
            for(let a=0;a<treeList.length;a++){
              let temp = treeList[a];
              this.jcheckbox.options.push({
                value:temp.id,
                label:temp.agentName
              })
            }
          }
        });
      },
      queryAgentByUserId(userId){
        let param = {userId:userId}
        getAction('/electronchanneluser/electronChannelUser/queryAgentByUserId',param).then((res)=>{
          if(res.success){
            this.jcheckbox.values = res.message
          }
        });
      }
    }
  }
</script>