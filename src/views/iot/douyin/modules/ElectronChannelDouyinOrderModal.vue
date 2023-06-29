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

        <a-form-item label="代理ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusId', validatorRules.cusId]" placeholder="请输入代理ID"></a-input>
        </a-form-item>
        <a-form-item label="产品通道ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'agentId', validatorRules.agentId]" placeholder="请输入产品通道ID"></a-input>
        </a-form-item>
        <a-form-item label="客户姓名" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusName', validatorRules.cusName]" placeholder="请输入客户姓名"></a-input>
        </a-form-item>
        <a-form-item label="客户手机号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusPhone', validatorRules.cusPhone]" placeholder="请输入客户手机号"></a-input>
        </a-form-item>
        <a-form-item label="身份证号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cusIdno', validatorRules.cusIdno]" placeholder="请输入身份证号"></a-input>
        </a-form-item>
        <a-form-item label="省" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'province', validatorRules.province]" placeholder="请输入省"></a-input>
        </a-form-item>
        <a-form-item label="市" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'city', validatorRules.city]" placeholder="请输入市"></a-input>
        </a-form-item>
        <a-form-item label="区" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'district', validatorRules.district]" placeholder="请输入区"></a-input>
        </a-form-item>
        <a-form-item label="详细地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'detailAddr', validatorRules.detailAddr]" placeholder="请输入详细地址"></a-input>
        </a-form-item>
        <a-form-item label="店铺id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'shopId', validatorRules.shopId]" placeholder="请输入店铺id"></a-input>
        </a-form-item>
        <a-form-item label="商户名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'shopName', validatorRules.shopName]" placeholder="请输入商户名称"></a-input>
        </a-form-item>
        <a-form-item label="店铺订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderId', validatorRules.orderId]" placeholder="请输入店铺订单号"></a-input>
        </a-form-item>
        <a-form-item label="下单端(0、站外 1、火山 2、抖音 3、头条 4、西瓜 5、微信 6、值点app 7、头条lite 8、懂车帝 9、皮皮虾 11、抖音极速版 12、TikTok 13、musically 14、穿山甲 15、火山极速版 16、服务市场 26、番茄小说 27、UG教育营销电商平台 28、Jumanji 29、电商SDK)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'bType', validatorRules.bType]" placeholder="请输入下单端(0、站外 1、火山 2、抖音 3、头条 4、西瓜 5、微信 6、值点app 7、头条lite 8、懂车帝 9、皮皮虾 11、抖音极速版 12、TikTok 13、musically 14、穿山甲 15、火山极速版 16、服务市场 26、番茄小说 27、UG教育营销电商平台 28、Jumanji 29、电商SDK)"></a-input>
        </a-form-item>
        <a-form-item label="小程序ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appId', validatorRules.appId]" placeholder="请输入小程序ID"></a-input>
        </a-form-item>
        <a-form-item label="支付类型 (0、货到付款 1 、微信 2、支付宝 3、小程序 4、银行卡 5、余额 7、无需支付（0元单） 8、DOU分期（信用支付） 9、新卡支付 12、先用后付)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'payType', validatorRules.payType]" placeholder="请输入支付类型 (0、货到付款 1 、微信 2、支付宝 3、小程序 4、银行卡 5、余额 7、无需支付（0元单） 8、DOU分期（信用支付） 9、新卡支付 12、先用后付)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="支付金额(分)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'payAmount', validatorRules.payAmount]" placeholder="请输入支付金额(分)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="订单状态(1:拉取成功，2:解密成功，3:同步成功 )" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'orderStatus', validatorRules.orderStatus]" placeholder="请输入订单状态(1:拉取成功，2:解密成功，3:同步成功 )"></a-input>
        </a-form-item>
        <a-form-item label="抖音下单日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择抖音下单日期" v-decorator="[ 'douyinCreateTime', validatorRules.douyinCreateTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="抖音商品ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'productId', validatorRules.productId]" placeholder="请输入抖音商品ID"></a-input>
        </a-form-item>
        <a-form-item label="商品skuId" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'skuId', validatorRules.skuId]" placeholder="请输入商品skuId"></a-input>
        </a-form-item>
        <a-form-item label="直播主播ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'authorId', validatorRules.authorId]" placeholder="请输入直播主播ID"></a-input>
        </a-form-item>
        <a-form-item label="直播主播名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'authorName', validatorRules.authorName]" placeholder="请输入直播主播名称"></a-input>
        </a-form-item>
        <a-form-item label="下单来源( 0、其他 1、直播间)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'themeType', validatorRules.themeType]" placeholder="请输入下单来源( 0、其他 1、直播间)"></a-input>
        </a-form-item>
        <a-form-item label="直播间ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'roomId', validatorRules.roomId]" placeholder="请输入直播间ID"></a-input>
        </a-form-item>
        <a-form-item label="内容id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'contentId', validatorRules.contentId]" placeholder="请输入内容id"></a-input>
        </a-form-item>
        <a-form-item label="视频id" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'videoId', validatorRules.videoId]" placeholder="请输入视频id" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="流量来源id(1:已发货未签收通知，2:已签收未激活通知，3:作废通知)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number v-decorator="[ 'originId', validatorRules.originId]" placeholder="请输入流量来源id(1:已发货未签收通知，2:已签收未激活通知，3:作废通知)" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="创建者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'createBy', validatorRules.createBy]" placeholder="请输入创建者"></a-input>
        </a-form-item>
        <a-form-item label="创建时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择创建时间" v-decorator="[ 'createTime', validatorRules.createTime]" :trigger-change="true" style="width: 100%"/>
        </a-form-item>
        <a-form-item label="更新者" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'updateBy', validatorRules.updateBy]" placeholder="请输入更新者"></a-input>
        </a-form-item>
        <a-form-item label="更新时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择更新时间" v-decorator="[ 'updateTime', validatorRules.updateTime]" :trigger-change="true" style="width: 100%"/>
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

  export default {
    name: "ElectronChannelDouyinOrderModal",
    components: { 
      JDate,
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
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
        validatorRules: {
          cusId: {rules: [
          ]},
          agentId: {rules: [
          ]},
          cusName: {rules: [
          ]},
          cusPhone: {rules: [
          ]},
          cusIdno: {rules: [
          ]},
          province: {rules: [
          ]},
          city: {rules: [
          ]},
          district: {rules: [
          ]},
          detailAddr: {rules: [
          ]},
          shopId: {rules: [
          ]},
          shopName: {rules: [
          ]},
          orderId: {rules: [
          ]},
          bType: {rules: [
          ]},
          appId: {rules: [
          ]},
          payType: {rules: [
          ]},
          payAmount: {rules: [
          ]},
          orderStatus: {rules: [
          ]},
          douyinCreateTime: {rules: [
          ]},
          productId: {rules: [
          ]},
          skuId: {rules: [
          ]},
          authorId: {rules: [
          ]},
          authorName: {rules: [
          ]},
          themeType: {rules: [
          ]},
          roomId: {rules: [
          ]},
          contentId: {rules: [
          ]},
          videoId: {rules: [
          ]},
          originId: {rules: [
          ]},
          createBy: {rules: [
          ]},
          createTime: {rules: [
          ]},
          updateBy: {rules: [
          ]},
          updateTime: {rules: [
          ]},
        },
        url: {
          add: "/douyin/electronChannelDouyinOrder/add",
          edit: "/douyin/electronChannelDouyinOrder/edit",
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
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'cusId','agentId','cusName','cusPhone','cusIdno','province','city','district','detailAddr','shopId','shopName','orderId','bType','appId','payType','payAmount','orderStatus','douyinCreateTime','productId','skuId','authorId','authorName','themeType','roomId','contentId','videoId','originId','createBy','createTime','updateBy','updateTime'))
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
        this.form.setFieldsValue(pick(row,'cusId','agentId','cusName','cusPhone','cusIdno','province','city','district','detailAddr','shopId','shopName','orderId','bType','appId','payType','payAmount','orderStatus','douyinCreateTime','productId','skuId','authorId','authorName','themeType','roomId','contentId','videoId','originId','createBy','createTime','updateBy','updateTime'))
      },

      
    }
  }
</script>