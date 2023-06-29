
<template>
  <div>
    <a-modal v-model="visible" :title="title" @ok="handleOk">
      <div style="width: 100%;height: 100%;position: relative;padding: 8px;border: 0px solid #d9d9d9;border-radius: 4px;">
      <!--<img style="width: 100%;" :src="imgUrl"  :preview="dataSource[0].key">-->
      <img style="width: 100%;" :src="imgUrl">
      </div>
    </a-modal>
  </div>
</template>

<script>
  import { getAction } from '@/api/manage'
  export default {
    name: "ElectronQrCodeModel",
    components: {
    },
    data () {
      return {
        title:'',
        imgUrl:'',
        visible: false,
        url: {
          getQrcode:"/wechatpay/iotWechatPay/generaQrCode", // 引入生成添加用户情况下的url
        },
      }
    },
    created () {
    },
    computed:{
    },
    methods: {
      show(id) {
        getAction(this.url.getQrcode+"/"+id, null).then((res) => {
          if (res.success) {
            this.imgUrl=res.result.qrcodeUrl;
          }else{
            this.$message.warning(res.message);
          }
        });
      },
      showModal() {
        this.visible = true;
      },
      handleOk(e) {
        console.log(e);
        this.visible = false;
      },
    }
  }
</script>

<style scoped>
  .avatar-uploader > .ant-upload {
    width:104px;
    height:104px;
  }
  .ant-upload-select-picture-card i {
    font-size: 49px;
    color: #999;
  }

  .ant-upload-select-picture-card .ant-upload-text {
    margin-top: 8px;
    color: #666;
  }

  .ant-table-tbody .ant-table-row td{
    padding-top:10px;
    padding-bottom:10px;
  }

</style>