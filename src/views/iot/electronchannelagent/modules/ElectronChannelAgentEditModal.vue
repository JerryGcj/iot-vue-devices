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
        <a-form-item label="通道名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'agentName', validatorRules.agentName]" placeholder="请输入通道名称"></a-input>
        </a-form-item>
        <a-form-item label="套餐代码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'packageCode', validatorRules.packageCode]" placeholder="请输入套餐代码"></a-input>
        </a-form-item>
        <a-form-item label="返佣条件" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'rakeBackTag', validatorRules.rakeBackTag]" placeholder="请输入返佣条件" allow-clear>
            <a-select-option value="0">不返</a-select-option>
            <a-select-option value="1">按首充</a-select-option>
            <a-select-option value="2">按激活</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'remark', validatorRules.remark]" placeholder="请输入通道名称"></a-input>
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
        name: "ElectronChannelAgentEditModal",
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
                    // agentSimpleName: {
                    //     rules: [
                    //         { required: true, message: '请填写代理商简称缩写!'}
                    //     ]
                    // },
                    agentName: {
                        rules: [
                            { required: true, message: '请填写通道名称!'}
                        ]
                    },
                    packageCode: {
                        rules: [
                            { required: true, message: '请填写套餐编码!'}
                        ]
                    },
                    rakeBackTag: {
                      rules: [
                        { required: true, message: '请填写返佣条件!'}
                      ]
                    },
                  remark:{}

                },
                url: {
                    add: "/electronchannelagent/electronChannelAgent/add",
                    edit: "/electronchannelagent/electronChannelAgent/edit",
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
                console.log(record)
                this.form.resetFields();
                this.model = Object.assign({}, record);
                this.visible = true;
                this.model.visible = true;
                this.$nextTick(() => {
                    this.form.setFieldsValue(pick(this.model,'agentName','packageCode','rakeBackTag','remark'))
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
                        formData.cancelMsg = ""
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
                this.form.setFieldsValue(pick(row,'agentName','packageCode','rakeBackTag','remark'))
            },


        }
    }
</script>