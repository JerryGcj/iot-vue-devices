<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="5" :sm="6">
            <a-form-item label="公众号名称">
              <a-input placeholder="请输入公众号名称" v-model="queryParam.mchName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button @click="handleAdd" type="primary" icon="plus" style="margin-left: 8px">新增</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="setTheDefault" type="primary">设置默认公众号</a-button>
      <!--<a-button type="primary" icon="download" @click="handleExportXls('iot_wechat_pay')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>-->
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"

        @change="handleTableChange">

        <template slot="showToolbar" slot-scope="text, record, index">
          <a-switch checkedChildren="是" unCheckedChildren="否" v-model="record.showToolbar" @change="showToolbarChange" @click="showToolbarSwitch(record.id)" />
        </template>

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无此图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="图片不存在" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无此文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="uploadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
          <a @click="viewQrcode(record)">查看二维码<a-divider type="vertical" /></a>
          <a @click="handleUploadLongImages(record)">上传长图<a-divider type="vertical" /></a>
          <a @click="handleUploadKeHuImage(record)">客户群图片<a-divider type="vertical" /></a>
          <a @click="handleEditNotice(record)">公告设置<a-divider type="vertical" /></a>
          <a @click="handleEditCustomerService(record)">客服设置<a-divider type="vertical" /></a>
<!--          <a @click="handleImport(record)">上传公众号图片<a-divider type="vertical"/></a>-->
          <a @click="handleEdit(record)">编辑</a>

          <!--<a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>-->
        </span>

      </a-table>


      <a-modal v-model:visible="visible" title="上传公众号长图" width="800px" :body-style="modalStyle" @ok="handleOk">
        <a-upload
          :action=uploadFileMinio
          :headers="headers"
          :file-list="fileList"
          :limit=20
          :multiple = true
          @change = "handleChange"
          @preview="handlePreview"
          list-type="picture-card"
        >
          <div v-if="fileList.length < 6">
            <div style="margin-top: 8px">点击上传</div>
          </div>
        </a-upload>
      </a-modal>

      <a-modal v-model:visible="visibleKeHu" title="上传公众号客户群图片" width="800px" :body-style="modalStyle" @ok="handleOkKeHu">
        <a-upload
          :action=uploadFileMinio
          :headers="headers"
          :file-list="fileList"
          :limit=1
          :multiple = true
          @change = "handleChange"
          @preview="handlePreview"
          list-type="picture-card"
        >
          <div v-if="fileList.length < 1">
            <div style="margin-top: 8px">点击上传</div>
          </div>
        </a-upload>
      </a-modal>

      <a-modal :visible="previewVisible" :title="previewTitle" :footer="null" :key=Math.random() @cancel="handleCancel">
        <img alt="example" style="width: 100%" :src="previewImage" />
      </a-modal>



      <a-modal v-model:visible="visibleNotice" title="公告编辑" width="800px" :body-style="modalStyle"  @ok="handleOkNotice">
        <a-textarea v-model:value="NoticeValue" :autosize="{ minRows:10}" placeholder="请输入公告内容并以英文分号（;）分隔，每条公告不超过17个字，包括标点符号" />
      </a-modal>

      <a-modal :visible="visibleCustomerService" title="客服设置" width="800px" :body-style="modalStyle"  @ok="handleOkCustomerService" @cancel="handleCustomerServiceUrlCancel">
        <a-textarea v-model="customerServiceUrl" :autosize="{ minRows:10}" placeholder="请输入客服url" />
      </a-modal>

    </div>

    <iotWechatPay-modal ref="modalForm" @ok="modalFormOk"></iotWechatPay-modal>
    <qr-code-modal ref="qrCodeModal" @ok="modalFormOk"></qr-code-modal>
    <iot-wechat-pay-import-picture-modal ref="importPictureModal" @ok="modalFormOk"></iot-wechat-pay-import-picture-modal>
  </a-card>


</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotWechatPayModal from './modules/IotWechatPayModal'
  import {getAction, postAction, putAction} from '@/api/manage'
  import QrCodeModal from "./modules/QrCodeModal";
  import IotWechatPayImportPictureModal from "./modules/IotWechatPayImportPictureModal";
  import ElectronOrderImportPictureModal from "../electronchannelagent/modules/ElectronOrderImportPictureModal";
  import { ACCESS_TOKEN } from "@/store/mutation-types"
  import Vue from 'vue'

  export default {
    name: "IotWechatPayList",
    mixins:[JeecgListMixin],
    components: {
      ElectronOrderImportPictureModal,
      QrCodeModal,
      IotWechatPayModal,
      IotWechatPayImportPictureModal,
    },
    data () {

      return {
        id:"",
        customerServiceUrl: "",
        previewTitle:'',
        showToolbarChecked:"",
        modalStyle: {
          height:'300px',
          overflowY: 'auto'
        },
        previewVisible: false,
        previewImage: '',
        visible: false,
        visibleKeHu: false,
        visibleNotice: false,
        visibleCustomerService: false,
        NoticeValue: '',
        //headers 手动添加token
        headers: {},
        tempId:'',
        fileList: [],
        endFileList: [],

        uploadFileMinio: window._CONFIG['domianURL'] + "/wechatpay/iotWechatPay/uploadLongPictures", // 上传文件服务器地址

        description: 'iot_wechat_pay管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'公众号名称',
            align:"center",
            dataIndex: 'mchName'
          },
          {
            title:'APPID',
            align:"center",
            dataIndex: 'appId'
          },
          /*{
            title:'开发者秘钥',
            align:"center",
            dataIndex: 'appSecret'
          },*/
          {
            title:'商户号',
            align:"center",
            dataIndex: 'mchId'
          },
          /*{
            title:'商户秘钥',
            align:"center",
            dataIndex: 'mchKey'
          },*/
          {
            title:'绑定域名',
            align:"center",
            dataIndex: 'domainName'
          },
          {
            title: '是否显示公众号底部菜单',
            align: "center",
            width: 60,
            scopedSlots: {customRender: 'showToolbar'},
          },
          // {
          //   title:'是否默认',
          //   align:"center",
          //   dataIndex: 'isdefault',
          //   customRender:(text)=>{
          //     if(text==0){
          //       return "是";
          //     }else if(text==1){
          //       return "否";
          //     }else {
          //       return text;
          //     }
          //   }
          // },
          // {
          //   title:'公众号图片',
          //   align:"center",
          //   dataIndex: 'imgUrl',
          //   customRender: function (t,r, index) {
          //     if (t !== null && t !== "" && t !== undefined) {
          //       return <img src={t} style="width: 200px; height: 100px; "/>
          //     }
          //   }
          // },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/wechatpay/iotWechatPay/list",
          delete: "/wechatpay/iotWechatPay/delete",
          deleteBatch: "/wechatpay/iotWechatPay/deleteBatch",
          exportXlsUrl: "/wechatpay/iotWechatPay/exportXls",
          importExcelUrl: "wechatpay/iotWechatPay/importExcel",
          setTheDefault: "/wechatpay/iotWechatPay/setTheDefault",
          editUrl: "/wechatpay/iotWechatPay/edit"
        },
        dictOptions:{
        },
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      handleAdd: function () {
        this.$refs.modalForm.add(false);
        this.$refs.modalForm.title = "新增";
        this.$refs.modalForm.disableSubmit = false;
      },
      showToolbarSwitch(id){
        const that = this;
        if (this.filterFlagChecked === true) {
          this.filterFlagChecked = "1"
        } else {
          this.filterFlagChecked = "0"
        }
        console.log(this.filterFlagChecked);
        debugger
        let showToolbar = this.showToolbarChecked;
        if (showToolbar) {
          showToolbar = "1"
        } else {
          showToolbar = "0"
        }
        putAction(this.url.editUrl,{showToolbar: showToolbar,id: id}).then((res)=>{
          if(res.success){
            that.$message.success(res.message);
            that.$emit('ok');
            // this.initData();
          }else{
            that.$message.warning(res.message);
          }
        });
      },

      showToolbarChange(checked) {
        this.showToolbarChecked = checked;
      },
      //上传长图 上传状态变更处理方法
      handleChange(info){
        this.fileList = info.fileList;
        console.log(info.file.status);
        console.log("====================");
        console.log(info.fileList);
        if (info.file.status === 'done'){
          for (let i = 0; i < info.fileList.length; i++) {
            if (this.fileList[i].response !== undefined){
              this.fileList[i].url = this.fileList[i].response.result.url;
            }
          }
        }
      },
      initDictConfig(){
      },
      setTheDefault: function () {
        //有选中的就不在查询后台
        if(this.selectedRowKeys.length!=1){
          this.$message.warning('请选择一个公众号！');
          return;
        }else{
          getAction(this.url.setTheDefault+"?id="+this.selectedRowKeys, null).then((res) => {
            if (res.success) {
              this.$message.success('设置成功');
              this.loadData();
              this.onClearSelected();
            }
          })
        }
      },
      viewQrcode: function (record) {
        this.$refs.qrCodeModal.visible = true;
        this.$refs.qrCodeModal.title = "【"+record.mchName+"】二维码";
        this.$refs.qrCodeModal.show(record.id);
      },
      handleEdit: function (record) {
        this.$refs.modalForm.title = "编辑";
        this.$refs.modalForm.departDisabled = true;
        this.$refs.modalForm.disableSubmit = false;
        this.$refs.modalForm.edit(record);
        this.$refs.modalForm.readonly = true;
      },
      //点击 公告编辑 弹出框
      handleEditNotice:function (record) {
        this.tempId = record.id;
        this.visibleNotice = true;
        this.NoticeValue = record.notice;
      },
      handleEditCustomerService:function (record) {
        debugger
        this.id = record.id;
        this.visibleCustomerService = true;
        this.customerServiceUrl = record.customerServiceUrl;
      },
      //点击 上传长图 后弹出对话框以及回显图片
      handleUploadLongImages: function (record) {
        if (record.longImgUrls !== null && record.longImgUrls !== undefined && record.longImgUrls !== ''){
          this.fileList = JSON.parse(record.longImgUrls)
        }else {
          this.fileList = [];
        }
        this.tempId = record.id;
        this.visible = true;
        const token = Vue.ls.get(ACCESS_TOKEN);
        this.headers = {"X-Access-Token":token}
      },
      //点击 上传客户 后弹出对话框以及回显图片
      handleUploadKeHuImage: function (record) {
        console.log(record)
        if (record.communicateGroupUrl !== null && record.communicateGroupUrl !== undefined && record.communicateGroupUrl !== ''){
          this.fileList = JSON.parse(record.communicateGroupUrl)
        }else {
          this.fileList = [];
        }
        this.tempId = record.id;
        this.visibleKeHu = true;
        const token = Vue.ls.get(ACCESS_TOKEN);
        this.headers = {"X-Access-Token":token}
      },

      //上传长图的确定处理
      handleOk: function (){
        this.endFileList = [];
        //处理数据格式
        for (let i = 0; i < this.fileList.length; i++) {
          this.endFileList.push({['url']:this.fileList[i].url,["name"]:this.fileList[i].name,["status"]:this.fileList[i].status,["uid"]:this.fileList[i].uid})
        }
        var editDate = {};
        editDate.id = this.tempId;
        editDate.longImgUrls = JSON.stringify(this.endFileList);
        putAction(this.url.editUrl, editDate).then((res) => {
          if (res.success) {
            this.$message.success(res.message);
            this.endFileList = [];
            this.fileList = [];
            this.visible = false;
          } else {
            this.$message.warning(res.message)
          }
          this.loadData(1);
        })
      },


      //上传客户群图的确定处理
      handleOkKeHu: function (){
        console.log(this.fileList)
        this.endFileList = [];
        //处理数据格式
        for (let i = 0; i < this.fileList.length; i++) {
          this.endFileList.push({['url']:this.fileList[i].url,["name"]:this.fileList[i].name,["status"]:this.fileList[i].status,["uid"]:this.fileList[i].uid})
        }
        var editDate = {};
        editDate.id = this.tempId;
        editDate.communicateGroupUrl = JSON.stringify(this.endFileList);
        putAction(this.url.editUrl, editDate).then((res) => {
          if (res.success) {
            this.$message.success(res.message);
            this.endFileList = [];
            this.fileList = [];
            this.visibleKeHu = false;
          } else {
            this.$message.warning(res.message)
          }
          this.loadData(1);
        })
      },
      //公告编辑的确定处理
      handleOkNotice: function (){
        var editDate = {};
        editDate.id = this.tempId;
        editDate.notice = this.NoticeValue;
        putAction(this.url.editUrl, editDate).then((res) => {
          if (res.success) {
            this.$message.success(res.message);
            this.visibleNotice = false;
          } else {
            this.$message.warning(res.message)
          }
          this.loadData(1);
        })
      },

      handleOkCustomerService: function (){
        var editDate = {};
        editDate.id = this.id;
        editDate.customerServiceUrl = this.customerServiceUrl;
        putAction(this.url.editUrl, editDate).then((res) => {
          if (res.success) {
            this.$message.success(res.message);
            this.visibleCustomerService = false;
          } else {
            this.$message.warning(res.message)
          }
          this.loadData(1);
        })
      },

      handleImport(record) {
        this.$refs.importPictureModal.show();
        this.$refs.importPictureModal.id = record.id;
      },
      getBase64(file) {
        return new Promise((resolve, reject) => {
          const reader = new FileReader();
          reader.readAsDataURL(file);
          reader.onload = () => resolve(reader.result);
          reader.onerror = error => reject(error);
        });
  },
      handleCancel() {
        this.previewVisible = false;
        this.previewTitle = '';
      },
      handleCustomerServiceUrlCancel() {
        this.visibleCustomerService = false;
      },
      async handlePreview(file) {
        if (!file.url && !file.preview) {
          file.preview = await this.getBase64(file.originFileObj);
        }
        this.previewImage = file.url || file.preview;
        this.previewVisible = true;
        this.previewTitle = file.name || file.url.substring(file.url.lastIndexOf('/') + 1);
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';

  /* you can make up upload button and sample style by using stylesheets */
  .ant-upload-select-picture-card i {
    font-size: 32px;
    color: #999;
  }

  .ant-upload-select-picture-card .ant-upload-text {
    margin-top: 8px;
    color: #666;
  }
</style>