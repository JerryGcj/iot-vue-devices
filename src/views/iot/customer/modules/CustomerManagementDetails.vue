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
          label="用户id">
          <a-input placeholder="用户id" v-decorator="['id', {}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="公司名称">
          <a-input placeholder="公司名称" v-decorator="['userCompany', {}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="公司类型">
            <a-radio-group  buttonStyle="solid"  v-model="userType" v-decorator="[ 'userType', validatorRules.userType]">
              <a-radio-button value="0">内部员工</a-radio-button>
              <a-radio-button value="1">代理商</a-radio-button>
              <a-radio-button value="2">合伙人</a-radio-button>
              <a-radio-button value="3">企业用户</a-radio-button>
              <a-radio-button value="4">{{this.model.userFlag=='0'?"内部电渠代理商":(this.model.userFlag=='1'?"外部电渠代理商":"电渠代理商")}}</a-radio-button>
            </a-radio-group>
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="联系人">
          <a-input  placeholder="联系人" v-decorator="['realname', {}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="联系电话">
          <a-input placeholder="联系电话" v-decorator="['phone', {}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="余额">
          <a-input placeholder="余额" v-decorator="['balance', {}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="备注">
          <a-input placeholder="备注" v-decorator="['note', {}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="创建人">
          <a-input placeholder="创建人" v-decorator="['createBy', {}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="创建时间">
          <a-input placeholder="创建时间" v-decorator="['createTime', {}]" />
        </a-form-item>

        <!--<a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="更新时间">
          <a-input placeholder="更新时间" v-decorator="['updateTime', {}]" />
        </a-form-item>-->

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上游通道ID">
          <a-input placeholder="上游通道ID" v-decorator="['channelId', {}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="接口ip白名单">
          <a-input placeholder="接口ip白名单" v-decorator="['ipWhite', {}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="密钥">
          <!--<a-input placeholder="密钥" v-decorator="['theKey', {}]" />-->
          <a-textarea placeholder="密钥" v-decorator="['theKey', {}]"></a-textarea>
        </a-form-item>

        <!--<a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="微信公众号地址">
          <a-input placeholder="微信公众号地址" v-decorator="['wechatAddress', {}]" />
        </a-form-item>-->

      </a-form>
    </a-spin>
<!--    <a-button v-if="userId === 'ae95dbad4e2e444bb6a1fce9582aac5f' || userId === '6469a4135f239e68058d92dc894d9935'" type="primary" @click="handleOk">确定</a-button>-->
    <a-button type="primary" @click="handleCancel">关闭</a-button>
  </a-drawer>
</template>

<script>
  import { httpAction,getAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"

  export default {
    name: "CustomerManagementDetails",
    data () {
      return {
        userId: '',
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
          add: "/customermanagement/customerManagement/add",
          edit: "/customermanagement/customerManagement/edit",
          getUserIdByTokenUrl:"sys/user/getUserId",
        },
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        let params = {}
        //获取当前登陆用户的信息
        getAction(this.url.getUserIdByTokenUrl, params).then((res) => {
          if (res.success) {
            this.userId=res.result.id;
          }
        });

        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'id','username','realname','password','salt','avatar','sex','email','phone','orgCode','status','delFlag','activitiSync','workNo','post','telephone','userCompany','userType','founderId','subordinatePartnerId','note','profitShare','channelId','payPassword','ipWhite','theKey','wechatAddress','balance','createBy','createTime','userFlag','updateTime'))
		  //时间格式化
          this.form.setFieldsValue({birthday:this.model.birthday?moment(this.model.birthday):null})
        });

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
            //时间格式化
            formData.birthday = formData.birthday?formData.birthday.format('YYYY-MM-DD HH:mm:ss'):null;

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