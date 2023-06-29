<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6">
            <a-form-item label="iccid">
              <a-input placeholder="请输入iccid" v-model="queryParam.iccid"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="手机号">
              <a-input placeholder="请输入手机号" v-model="queryParam.mobile"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="openId">
              <a-input placeholder="请输入openId" v-model="queryParam.openId"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="运营商">
              <j-dict-select-tag placeholder="请选择运营商" v-model="queryParam.operatorId" dict-code="iot_operator,operator_name,id,state=1"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="退款状态">
              <j-dict-select-tag placeholder="请选择退款状态" v-model="queryParam.refundStatus" dict-code="refund_status"></j-dict-select-tag>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button type="primary" icon="download" @click="handleExportXls('用户退款记录')" style="margin-left: 8px" :disabled="isDisable">导出</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <!--<div class="table-operator">-->
      <!--<a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>-->
      <!--<a-button type="primary" icon="download" @click="handleExportXls('iot_refund_record')">导出</a-button>-->
      <!--<a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
        <!--<a-button type="primary" icon="import">导入</a-button>-->
      <!--</a-upload>-->
      <!--<a-dropdown v-if="selectedRowKeys.length > 0">-->
        <!--<a-menu slot="overlay">-->
          <!--<a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>-->
        <!--</a-menu>-->
        <!--<a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>-->
      <!--</a-dropdown>-->
    <!--</div>-->

    <!-- table区域-begin -->
    <div>
      <!--<div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">-->
        <!--<i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项-->
        <!--<a style="margin-left: 24px" @click="onClearSelected">清空</a>-->
      <!--</div>-->

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"


        @change="handleTableChange">
        <span slot="iccid" slot-scope="iccid, record">
            <a v-text="iccid" @click="packageList(record)"></a>
        </span>
        <span slot="account" slot-scope="account,record">
            <a v-text="account" @click="accountComsumer(record)"></a>
        </span>
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
           <a v-if="record.refundStatus==0 || record.refundStatus==2" v-has="'refund:pass'" @click="pass(record)">审核</a>
          <!--<a-popconfirm title="确定驳回吗?" @confirm="() => rejected(record,2)">
            <a v-if="record.refundStatus==0" v-has="'refund:rejected'"><a-divider type="vertical" />驳回</a>
          </a-popconfirm>-->
          <!--<a @click="handleEdit(record)">编辑</a>-->

          <!--<a @click="pass(record,2)">驳回</a>-->
          <!--<a-dropdown>-->
            <!--<a class="ant-dropdown-link">更多 <a-icon type="down" /></a>-->
            <!--<a-menu slot="overlay">-->
              <!--<a-menu-item>-->
                <!--<a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">-->
                  <!--<a>删除</a>-->
                <!--</a-popconfirm>-->
              <!--</a-menu-item>-->
            <!--</a-menu>-->
          <!--</a-dropdown>-->
        </span>

      </a-table>
    </div>
    <package-list-modal ref="packageListModal" @ok="modalFormOk"></package-list-modal>
    <mobile-package-list-modal ref="mobilePackageListModal" @ok="modalFormOk"></mobile-package-list-modal>
    <iotRefundRecord-modal ref="modalForm" @ok="modalFormOk"></iotRefundRecord-modal>
    <iot-account-consumer-record-modal ref="iotAccountConsumerRecord"></iot-account-consumer-record-modal>
  </a-card>
</template>

<script>
  import { getAction,downFile } from '@/api/manage'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotRefundRecordModal from './modules/IotRefundRecordModal'
  import PackageListModal from '@/views/iot/unicomcard/modules/PackageListModal'
  import MobilePackageListModal from '@/views/iot/card/modules/PackageListModal'
  import  IotAccountConsumerRecordModal from './modules/IotAccountConsumerRecordModal'
  export default {
    name: "IotRefundRecordList",
    mixins:[JeecgListMixin],
    components: {
      IotRefundRecordModal,PackageListModal,MobilePackageListModal,IotAccountConsumerRecordModal
    },
    data () {
      return {
        description: 'iot_refund_record管理页面',
        isDisable: false,
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
            title:'运营商',
            align:"center",
            dataIndex: 'operatorId_dictText'
          },
          {
            title:'用户手机号',
            align:"center",
            dataIndex: 'mobile'
          },
          /*{
            title:'openId',
            align:"center",
            dataIndex: 'openId'
          },*/
          {
            title:'iccid',
            align:"center",
            scopedSlots: {customRender: 'iccid'},
            dataIndex: 'iccid'
          },
          {
            title:'退款套餐',
            align:"center",
            dataIndex: 'packageName'
          },
          {
            title:'购买时间',
            align:"center",
            dataIndex: 'buyTime'
          },
          {
            title:'申请金额(元)',
            align:"center",
            dataIndex: 'refundMoney',
            scopedSlots: {customRender: 'account'}
          },
          {
            title:'实退金额(元)',
            align:"center",
            dataIndex: 'actualRefundMoney'
          },
          {
            title:'退款状态',
            align:"center",
            dataIndex: 'refundStatus_dictText'
          },
          {
            title:'退款渠道',
            align:"center",
            dataIndex: 'channel'
          },
          {
            title:'退款入账账户',
            align:"center",
            dataIndex: 'userReceivedAccount'
          },
          {
            title:'备注',
            align:"center",
            dataIndex: 'refundMsg'
          },
          {
            title:'用户类型',
            align:"center",
            dataIndex: 'isNew',
            customRender:function (text) {
              if(text=="0"){
                return "新用户";
              } else{
                return "老用户";
              }
            }
          },
          {
            title:'申请时间',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title:'退款成功时间',
            align:"center",
            dataIndex: 'successTime'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/refund/iotRefundRecord/list",
          auditRefundRecord: "/refund/iotRefundRecord/auditRefundRecord",
          rejected: "/refund/iotRefundRecord/rejected",
          deleteBatch: "/refund/iotRefundRecord/deleteBatch",
          exportXlsUrl: "/refund/iotRefundRecord/exportXls",
          importExcelUrl: "refund/iotRefundRecord/importExcel",
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
      packageList(record){
        if(record.operatorType == 2){
          this.$refs.packageListModal.edit(record.iccid);
        }else if (record.operatorType == 1){
          this.$refs.mobilePackageListModal.edit(record.iccid);
        }
      },
      accountComsumer(record){
        if(record.isNew == 0){
          //跳转到钱包消费明细
          this.$refs.iotAccountConsumerRecord.edit(record);
        }else{
          this.$message.warning("只有新用户才可以查询钱包余额消耗情况！");
        }
      },
      initDictConfig(){
      },
      pass(record){
        this.$refs.modalForm.edit(record);
        this.$refs.modalForm.title = "退款审核";
        this.$refs.modalForm.disableSubmit = false;
      },
      rejected(record,type){
        let refundStatus = record.refundStatus;
        if(refundStatus ==  1){
          this.$message.warning("已经审核成功，请勿重复操作！");
          return;
        }
        let params = {id:record.id,type:type};

        getAction(this.url.rejected,params).then((res)=>{
          if(res.success){
            this.$message.success(res.message);
            this.loadData();
          }else{
            this.$message.warning(res.message);
            this.loadData();
          }
        })
      },
      handleExportXls(fileName){
        this.isDisable = true;
        if(!fileName || typeof fileName != "string"){
          fileName = "导出文件"
        }
        let param = {...this.queryParam};
        if(this.selectedRowKeys && this.selectedRowKeys.length>0){
          param['selections'] = this.selectedRowKeys.join(",")
        }
        console.log("导出参数",param)
        downFile(this.url.exportXlsUrl,param).then((data)=>{
          setTimeout(() => {
            this.isDisable = false;
            if (!data) {
              this.$message.warning("文件下载失败")
              return
            }
            if (typeof window.navigator.msSaveBlob !== 'undefined') {
              window.navigator.msSaveBlob(new Blob([data]), fileName+'.xls')
            }else{
              let url = window.URL.createObjectURL(new Blob([data]))
              let link = document.createElement('a')
              link.style.display = 'none'
              link.href = url
              link.setAttribute('download', fileName+'.xls')
              document.body.appendChild(link)
              link.click()
              document.body.removeChild(link); //下载完成移除元素
              window.URL.revokeObjectURL(url); //释放掉blob对象
            }
          },1000)

        })
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>