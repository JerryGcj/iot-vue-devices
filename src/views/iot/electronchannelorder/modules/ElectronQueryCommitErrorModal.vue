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
          label="失败原因">
          <a-textarea  :rows="4" v-model="errorMsg"/>
        </a-form-item>

		
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"

  export default {
    name: "CardDistributionModal",
    data () {
      return {
        importBatch:'',
        errorMsg:'',
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

        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
        },
        url: {
          add: "/carddistribution/cardDistribution/add",
          edit: "/carddistribution/cardDistribution/edit",
        },
      }
    },
    created () {
    },
    methods: {
      add () {
        this.visible = true;
      },

      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.$emit('close');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      },


    }
  }
</script>

<style lang="less" scoped>

</style>