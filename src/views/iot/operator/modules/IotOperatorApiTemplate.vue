<template>
  <a-drawer
    :title="title"
    :width="width"
    placement="right"
    :closable="false"
    @close="close"
    :visible="visible">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="API模版" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select v-decorator="[ 'apiTemplate', validatorRules.apiTemplate]" v-model="apiTemplate" placeholder="请选择关联API模版">
<!--            <a-select-option value="API-RongHeImpl-0228">API-RongHeImpl-0228</a-select-option>-->
<!--            <a-select-option value="huThirdServiceImpl">huThirdServiceImpl</a-select-option>-->
            <a-select-option value="oneLinkServiceImpl">oneLinkServiceImpl</a-select-option>
<!--            <a-select-option value="jhThirdServiceImpl">jhThirdServiceImpl</a-select-option>-->
            <a-select-option value="iotGateway">iotGateway</a-select-option>
          </a-select>
        </a-form-item>

        <!--1.-->
        <a-form-item v-if="apiTemplate === 'API-RongHeImpl-0228'" label="商户ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'merchantsId', {}]" placeholder="请输入商户ID" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'API-RongHeImpl-0228'" label="秘钥" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'secretKey', {}]" placeholder="秘钥" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'API-RongHeImpl-0228'" label="URL前缀" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'urlPrefix', {}]" placeholder="URL前缀" style="width: 100%"/>
        </a-form-item>

        <!--2.-->
        <a-form-item v-if="apiTemplate === 'huThirdServiceImpl'" label="系统分配的AppId" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'app_id', {}]" placeholder="系统分配的AppId" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'huThirdServiceImpl'" label="appSecret" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appSecret', {}]" placeholder="appSecret" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'huThirdServiceImpl'" label="version接口版本号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'version', {}]" placeholder="version接口版本号" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'huThirdServiceImpl'" label="请求url" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'url', {}]" placeholder="请求url" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'huThirdServiceImpl'" label="账号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'username', {}]" placeholder="账号" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'huThirdServiceImpl'" label="密码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'password', {}]" placeholder="密码" style="width: 100%"/>
        </a-form-item>

        <!--3.-->
        <a-form-item v-if="apiTemplate === 'oneLinkServiceImpl'" label="应用编号(APPID)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appid', {}]" placeholder="应用编号(APPID)" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'oneLinkServiceImpl'" label="接入密码(password)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'password', {}]" placeholder="接入密码(password)" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'oneLinkServiceImpl'" label="接口地址前缀" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'url', {}]" placeholder="接口地址前缀" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'oneLinkServiceImpl'" label="token地址前缀" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'token_url', {}]" placeholder="token地址前缀" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'oneLinkServiceImpl'" label="版本号(版本号前加/)" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'version', {}]" placeholder="版本号(版本号前加/)" style="width: 100%"/>
        </a-form-item>

        <!--4.-->
        <a-form-item v-if="apiTemplate === 'jhThirdServiceImpl'" label="接口地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'url', {}]" placeholder="接口地址" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'jhThirdServiceImpl'" label="版本号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'version', {}]" placeholder="版本号" style="width: 100%"/>
        </a-form-item>

        <!--5.-->
        <a-form-item v-if="apiTemplate === 'iotGateway'" label="AppId" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appId', {}]" placeholder="请输入AppId" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'iotGateway'" label="APP SECRET" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'appSecret', {}]" placeholder="APP SECRET" style="width: 100%"/>
        </a-form-item>

        <a-form-item v-if="apiTemplate === 'iotGateway'" label="openId" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'openId', {}]" placeholder="openId" style="width: 100%"/>
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
  import { validateDuplicateValue } from '@/utils/util'
  import JDate from '@/components/jeecg/JDate'

  export default {
    name: "IotOperatorApiTemplate",
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
          operatorName:{
            rules: [{
              required: true, message: '请输入运营商名称!'
            }]
          },

          apiTemplate:{
            rules: [{
              required: true, message: '请选择API模版!'
            }]
          },


        },
        url: {
          add: "/iotoperator/iotOperator/add",
          edit: "/iotoperator/iotOperator/edit",
        }
      }
    },
    created () {
    },
    methods: {
      add (record) {

        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
      },
      edit (record) {

        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'apiTemplate'))
        })


        if(this.model.apiTemplate === 'huThirdServiceImpl'){

          this.apiTemplate =  'huThirdServiceImpl';

          this.model.id = this.model.operatorId;

          this.$nextTick(() => {
              this.form.setFieldsValue(pick(this.model,'apiTemplate','app_id','appSecret','version','url','username','password'))
          })
        }

        if(this.model.apiTemplate === 'API-RongHeImpl-0228'){

          this.model.id = this.model.operatorId;

          this.apiTemplate =  'API-RongHeImpl-0228';
          this.$nextTick(() => {
            this.form.setFieldsValue(pick(this.model,'apiTemplate','merchantsId','secretKey','urlPrefix'))
          })
        }

        if(this.model.apiTemplate === 'oneLinkServiceImpl'){

          this.model.id = this.model.operatorId;

          this.apiTemplate =  'oneLinkServiceImpl';
          this.$nextTick(() => {
            this.form.setFieldsValue(pick(this.model,'apiTemplate','appid','password','url','token_url','version'))
          })
        }

        if(this.model.apiTemplate === 'jhThirdServiceImpl'){

          this.model.id = this.model.operatorId;

          this.apiTemplate =  'jhThirdServiceImpl';
          this.$nextTick(() => {
            this.form.setFieldsValue(pick(this.model,'url','version'))
          })
        }
        if(this.model.iotGateway === 'iotGateway'){
          console.log('this.model', this.model);
          this.model.id = this.model.operatorId;
          this.apiTemplate =  'iotGateway';
          this.$nextTick(() => {
            this.form.setFieldsValue(pick(this.model,'appId','openId','appSecret'))
          })
        }

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

            if(values.apiTemplate == 'API-RongHeImpl-0228'){
                const formData = new FormData();

                formData.append('operatorId',this.model.id);
                formData.append('type',values.apiTemplate);
                formData.append('merchantsId',values.merchantsId);
                formData.append('secretKey',values.secretKey);
                formData.append('urlPrefix',values.urlPrefix);
                let httpurl = '/iotoperator/iotOperator/addTemplate';
                let method = 'post';
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


          if(values.apiTemplate == 'huThirdServiceImpl'){
            const formData = new FormData();

            formData.append('operatorId',this.model.id);
            formData.append('type',values.apiTemplate);

            formData.append('app_id',values.app_id);
            formData.append('appSecret',values.appSecret);
            formData.append('version',values.version);
            formData.append('url',values.url);
            formData.append('username',values.username);
            formData.append('password',values.password);

            let httpurl = '/iotoperator/iotOperator/addTemplate';
            let method = 'post';
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


          if(values.apiTemplate == 'oneLinkServiceImpl'){

            const formData = new FormData();

            formData.append('operatorId',this.model.id);
            formData.append('type',values.apiTemplate);

            formData.append('appid',values.appid);
            formData.append('password',values.password);
            formData.append('url',values.url);
            formData.append('token_url',values.token_url);
            formData.append('version',values.version);

            let httpurl = '/iotoperator/iotOperator/addTemplate';
            let method = 'post';
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

          if(values.apiTemplate == 'jhThirdServiceImpl'){

            const formData = new FormData();

            formData.append('operatorId',this.model.id);
            formData.append('type',values.apiTemplate);

            formData.append('url',values.url);
            formData.append('version',values.version);

            let httpurl = '/iotoperator/iotOperator/addTemplate';
            let method = 'post';
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
          if(values.apiTemplate == 'iotGateway'){
            const formData = new FormData();
            formData.append('operatorId',this.model.id);
            formData.append('type',values.apiTemplate);
            formData.append('openId',values.openId);
            formData.append('appId',values.appId);
            formData.append('appSecret',values.appSecret);
            formData.append('paramVersion',"1.0");
            formData.append('serverUrl',"https://gwapi.10646.cn/api/");
            formData.append('version',"V1.1");

            let httpurl = '/iotoperator/iotOperator/addTemplate';
            let method = 'post';
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
        }
        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'operatorName','operatorTypeId','state','createTime','threadNumber','queueNumber','concurrencyNumber','requestNumber','waitingSeconds','intervalTime','apiTemplate'))
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