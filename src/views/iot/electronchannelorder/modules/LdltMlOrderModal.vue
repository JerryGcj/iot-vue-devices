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

        <a-form-item label="触点编码，state=0 时有" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'touchApplyId', validatorRules.touchApplyId]"
                   placeholder="请输入触点编码，state=0 时有"></a-input>
        </a-form-item>
        <a-form-item label="product" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'product', validatorRules.product]" placeholder="请输入product"></a-input>
        </a-form-item>
        <a-form-item label="订购号码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'mobile', validatorRules.mobile]" placeholder="请输入订购号码"></a-input>
        </a-form-item>
        <a-form-item label="联系人号码，state=0 时有" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'contactNumber', validatorRules.contactNumber]"
                   placeholder="请输入联系人号码，state=0 时有"></a-input>
        </a-form-item>
        <a-form-item label="消息id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'messageId', validatorRules.messageId]" placeholder="请输入消息id"></a-input>
        </a-form-item>
        <a-form-item label="0 下单 1 激活 6 首充" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'state', validatorRules.state]" placeholder="请选择是否展示" allowClear>
            <a-select-option value="0">下单</a-select-option>
            <a-select-option value="1">激活</a-select-option>
            <a-select-option value="6">首充</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="创建时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择创建时间" v-decorator="[ 'time', validatorRules.time]" :trigger-change="true"
                  style="width: 100%"/>
        </a-form-item>
        <a-form-item label="商城订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderId', validatorRules.orderId]" placeholder="请输入商城订单号"></a-input>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

import {httpAction} from '@/api/manage'
import pick from 'lodash.pick'
import JDate from '@/components/jeecg/JDate'

export default {
  name: "LdltMlOrderModal",
  components: {
    JDate,
  },
  data() {
    return {
      form: this.$form.createForm(this),
      title: "操作",
      width: 800,
      visible: false,
      model: {},
      labelCol: {
        xs: {span: 24},
        sm: {span: 5},
      },
      wrapperCol: {
        xs: {span: 24},
        sm: {span: 16},
      },
      confirmLoading: false,
      validatorRules: {
        touchApplyId: {
          rules: []
        },
        product: {
          rules: []
        },
        mobile: {
          rules: []
        },
        contactNumber: {
          rules: []
        },
        messageId: {
          rules: []
        },
        state: {
          rules: []
        },
        time: {
          rules: []
        },
        orderId: {
          rules: []
        },
      },
      url: {
        add: "/ldltmlorder/ldltMlOrder/add",
        edit: "/ldltmlorder/ldltMlOrder/edit",
      }
    }
  },
  created() {
  },
  methods: {
    add() {
      this.edit({});
    },
    edit(record) {
      this.form.resetFields();
      this.model = Object.assign({}, record);
      this.visible = true;
      this.$nextTick(() => {
        this.form.setFieldsValue(pick(this.model, 'touchApplyId', 'product', 'mobile', 'contactNumber', 'messageId', 'state', 'time', 'orderId'))
      })
    },
    close() {
      this.$emit('close');
      this.visible = false;
    },
    handleOk() {
      const that = this;
      // 触发表单验证
      this.form.validateFields((err, values) => {
        if (!err) {
          that.confirmLoading = true;
          let httpurl = '';
          let method = '';
          if (!this.model.id) {
            httpurl += this.url.add;
            method = 'post';
          } else {
            httpurl += this.url.edit;
            method = 'put';
          }
          let formData = Object.assign(this.model, values);
          console.log("表单提交数据", formData)
          httpAction(httpurl, formData, method).then((res) => {
            if (res.success) {
              that.$message.success(res.message);
              that.$emit('ok');
            } else {
              that.$message.warning(res.message);
            }
          }).finally(() => {
            that.confirmLoading = false;
            that.close();
          })
        }

      })
    },
    handleCancel() {
      this.close()
    },
    popupCallback(row) {
      this.form.setFieldsValue(pick(row, 'touchApplyId', 'product', 'mobile', 'contactNumber', 'messageId', 'state', 'time', 'orderId'))
    },


  }
}
</script>