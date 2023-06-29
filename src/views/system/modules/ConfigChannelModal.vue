<template>
  <a-modal
    title="配置通道"
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
        <a-form-item label="用户账号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input placeholder="请输入用户账号" v-model="username" :readOnly="true"/>
        </a-form-item>

        <a-form-item label="选择产品" :labelCol="labelCol" :wrapperCol="wrapperCol">
            <a-select
              allowClear="true"
              v-model="operatorValue"
              mode="multiple"
              style="width: 100%"
              placeholder="请选择"
              :options="dictOperatorOptions"
              :filterOption="likeQuery"
              :autoClearSearchValue="false"
              :maxTagTextLength=3
              :maxTagCount=3
            ></a-select>
          </a-form-item>
      </a-form>
    </a-spin>
<!--    <a-table
      ref="table"
      size="middle"
      bordered
      rowKey="id"
      :columns="columns"
      :dataSource="dataSource"
      :pagination="ipagination"
      :loading="loading"
      :rowSelection="{selectedRowKeys: selectedRowKeys1, onChange: onSelectChange1, type:'radio'}"
      @change="handleTableChange">

    </a-table>-->
  </a-modal>
</template>

<script>
  import { postAction,getAction } from '@/api/manage'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import JCheckbox from '@/components/jeecg/JCheckbox'

  export default {
    name: "PasswordModal",
    mixins:[JeecgListMixin],
    components: {
      JCheckbox
    },
    data () {
      return {
        operatorValue: [],
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

        },
        jcheckbox: {
          values: '',
          options: []
        },
        columns: [
          {
            title: '通道缩写',
            align:"center",
            dataIndex: 'agentSimpleName'
          },
          {
            title: '通道名称',
            align:"center",
            dataIndex: 'agentName'
          },
          {
            title: '通道ID',
            align:"center",
            dataIndex: 'agentId'
          },
          {
            title: '套餐名称',
            align:"center",
            dataIndex: 'packageName'
          },
          {
            title: '归属地',
            align:"center",
            dataIndex: 'belongArea_dictText'
          },
          {
            title: '发展人工号',
            align:"center",
            dataIndex: 'devStaffNum'
          },
          {
            title: '存赠编码',
            align:"center",
            dataIndex: 'depositNum'
          },
          {
            title: '备注',
            align:"center",
            dataIndex: 'agentRemark'
          }
        ],
        url: {
          list: "/electronchannelagent/electronChannelAgent/list",
          edit: "/sys/telecomAgent/configProduct",
          initOperatorUrl: "/electronchannelagent/electronChannelAgent/initOperator",
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
    created () {
      console.log("created");
      this.initOperator();
    },

    methods: {
      likeQuery(input, option){
        return (option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0)
      },
      initOperator() {
        getAction(this.url.initOperatorUrl).then((res) => {
          if (res.success) {
            this.dictOperatorOptions = res.result
          }
        })
      },
      edit (record) {
        console.log(record.channelId)
        this.form.resetFields();
        this.queryLowerAgent();
        this.visible = true;
        this.userId = record.id;
        this.username = record.username;
        this.queryAgentByUserId(this.userId);
        this.selectedRowKeys1[0] = record.channelId
        this.$nextTick(() => {
          this.form.setFieldsValue();
        });
      },
      close () {
        this.$emit('close');
        this.visible = false;
        this.disableSubmit = false;
        this.currentAgentId = '';
        this.selectedRowKeys1 = [];
        this.selectionRows1 = [];
        this.operatorValue = []
      },
      handleSubmit () {
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            this.confirmLoading = true;
            let param = {userId:this.userId,agentIds:this.operatorValue.join(",")};
            postAction(this.url.edit,param).then((res)=>{
              if(res.success){
                this.$message.success(res.message);
                this.$emit('ok');
                this.loadData(1);
              }else{
                this.$message.warning(res.message);
              }
            }).finally(() => {
              this.confirmLoading = false;
              this.close();
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