<template>
  <a-modal
    :title="title"
    :width="500"
    :visible="visible"
    :confirmLoading="confirmLoading"
    :okButtonProps="{ props: {disabled: disableSubmit} }"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-card :bordered="false">
      <pre>{{apiInfo}}</pre>
    </a-card>
  </a-modal>
</template>

<script>

  export default {
    name: "CardDistributionModal",
    data () {
      return {
        importBatch:'',
        apiInfo:'',
        title:"",
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
        }
      }
    },
    created () {
    },
    methods: {
      add (record) {
        this.apiInfo = record;
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