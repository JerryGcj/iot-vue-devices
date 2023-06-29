<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="导入批次">
              <a-select  placeholder="请选择导入批次" v-model="queryParam.importBatch" :allowClear=true >
                <a-select-option v-for="(item,i) in importBatch" :value="item">
                  {{item}}
                </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="订单状态">
              <j-dict-select-tag placeholder="请选择订单状态" v-model="queryParam.orderStatus" dict-code="electron_order_status"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="客户姓名">
              <a-input placeholder="请输入客户姓名" v-model="queryParam.cusName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="客户手机号">
              <a-input placeholder="请输入客户手机号" v-model="queryParam.cusPhone"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="运单号">
              <a-input placeholder="请输入运单号" v-model="queryParam.expressNo"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="iccid">
              <a-input placeholder="请输入iccid" v-model="queryParam.iccid"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="电信号码">
              <a-input placeholder="请输入电信号码" v-model="queryParam.accessNumber"></a-input>
            </a-form-item>
          </a-col>
          <!--<template v-if="toggleSearchStatus">-->
          <!--</template>-->
          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="importOnlineExcel">
        <a-button type="primary" icon="import">导入飞鱼订单</a-button>
      </a-upload>
      <!--<a-button type="primary" icon="set" @click="autoDelivery()">自动提单</a-button>-->
      <a-button type="primary" icon="download" @click="exportExpressOrder('电信发货单')">导出电信发货单</a-button>

      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="syncExpressNoAndIccid" @change="handleImportExcel">
        <a-button type="primary" icon="import">更新电信单号及iccid</a-button>
      </a-upload>

      <a-button type="primary" icon="form" @click="sendSms">发送短信</a-button>

      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="accessNumberUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">更新接入号</a-button>
      </a-upload>

      <a-button type="primary" icon="form" @click="graphReport">查看激活</a-button>
      <!--<a-button  @click="handleAdd" type="primary" icon="plus">新增线下发货订单</a-button>-->

      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importOnlineOrder" @change="importOnlineExcel">
        <a-button type="primary" icon="import">导入线下发货订单</a-button>
      </a-upload>
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

        @change="handleTableChange">

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
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <electronChannelOrder-modal ref="modalForm" @ok="modalFormOk"></electronChannelOrder-modal>
    <electron-send-sms-modal ref="sendSmsModal" @ok="modalFormOk"></electron-send-sms-modal>
    <electron-graph-report-modal ref="graphReport" @ok="modalFormOk"></electron-graph-report-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronChannelOrderModal from './modules/ElectronChannelOrderModal'
  import ElectronSendSmsModal from './modules/ElectronSendSmsModal'
  import { postAction,getAction,downFile} from '@/api/manage'
  import ElectronGraphReportModal from "./modules/ElectronGraphReportModal";

  export default {
    name: "ElectronChannelOrderList",
    mixins:[JeecgListMixin],
    components: {
      ElectronGraphReportModal,
      ElectronChannelOrderModal,
      ElectronSendSmsModal
    },
    data () {
      return {
        description: 'electron_channel_order管理页面',
        queryParam:{
          "agentSimpleName": "jsth"
        },
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
            title:'客户姓名',
            align:"center",
            dataIndex: 'cusName'
          },
          {
            title:'客户手机号',
            align:"center",
            dataIndex: 'cusPhone'
          },
          {
            title:'手动填写地址',
            align:"center",
            dataIndex: 'manualAddr'
          },
          {
            title:'详细地址',
            align:"center",
            dataIndex: 'detailAddr'
          },
          {
            title:'订单状态',
            align:"center",
            dataIndex: 'orderStatus_dictText',
          },
          {
            title:'导入批次',
            align:"center",
            dataIndex: 'importBatch'
          },
          {
            title:'快递单号',
            align:"center",
            dataIndex: 'expressNo'
          },
          {
            title:'iccid',
            align:"center",
            dataIndex: 'iccid'
          },
          {
            title:'接入号',
            align:"center",
            dataIndex: 'accessNumber'
          },
          {
            title:'入网日期',
            align:"center",
            dataIndex: 'networkAccessTime'
          },
          // {
          //   title: '操作',
          //   dataIndex: 'action',
          //   align:"center",
          //   scopedSlots: { customRender: 'action' }
          // }
        ],
        url: {
          list: "/electronchannelorder/electronChannelOrder/list",
          delete: "/electronchannelorder/electronChannelOrder/delete",
          deleteBatch: "/electronchannelorder/electronChannelOrder/deleteBatch",
          exportXlsUrl: "/electronchannelorder/electronChannelOrder/exportXls",
          importExcelUrl: "electronchannelorder/electronChannelOrder/importExcel",
          queryImportBatch: "electronchannelorder/electronChannelOrder/queryImportBatch",
          syncExpressNoAndIccid: "electronchannelorder/electronChannelOrder/syncExpressNoAndIccid",
          accessNumberUrl: "electronchannelorder/electronChannelOrder/upDateAccessNumber",
          importOnlineOrder: "electronchannelorder/electronChannelOrder/importOnlineOrder"
        },
        dictOptions:{
        },
        importBatch:[],
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
      syncExpressNoAndIccid: function(){
        return `${window._CONFIG['domianURL']}/${this.url.syncExpressNoAndIccid}`;
      },
      accessNumberUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.accessNumberUrl}`;
      },
      importOnlineOrder: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importOnlineOrder}`;
      }
    },
    created() {
      this.queryImportBatch();
    },
    methods: {

      autoDelivery:function(){
        let batch = this.queryParam.importBatch;
        if(!batch){
          this.$message.warn("请选择要发货的批次！")
          return;
        }
        let params = {"importBatch":batch};
        postAction('electronchannelorder/electronChannelOrder/pushUpstreamOrder/',params).then((res) => {
          if (res.success) {
            this.$message.success(res.message)
          }else{
            this.$message.warn(res.message)
          }
        })

      },
      //导出发货单
      exportExpressOrder:function(fileName){
        let batch = this.queryParam.importBatch;
        if(!batch){
          this.$message.warn("请选择要导出的批次！")
          return;
        }
        if(!fileName || typeof fileName != "string"){
          fileName = "导出文件"
        }
        let param = {...this.queryParam};
        if(this.selectedRowKeys && this.selectedRowKeys.length>0){
          param['selections'] = this.selectedRowKeys.join(",")
        }
        console.log("导出参数",param)
        downFile(this.url.exportXlsUrl,param).then((data)=>{
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
            this.loadData();
            document.body.removeChild(link); //下载完成移除元素
            window.URL.revokeObjectURL(url); //释放掉blob对象
          }
        })
      },
      importOnlineExcel(info){
        console.log('info',info)
        if (info.file.status !== 'uploading') {
          console.log(info.file, info.fileList);
        }
        if (info.file.status === 'done') {
          if (info.file.response.success) {
            // this.$message.success(`${info.file.name} 文件上传成功`);
            if (info.file.response.code === 201) {
              let { message, result: { msg, fileUrl, fileName } } = info.file.response
              let href = window._CONFIG['domianURL'] + fileUrl
              this.$warning({
                  title: message,
                  content: (
                    <div>
                    <span>{msg}</span><br/>
                    <span>具体详情请 <a href={href} target="_blank" download={fileName}>点击下载</a> </span>
                </div>
              )
            })
            } else {
              this.$message.success(info.file.response.message || `${info.file.name} 文件上传成功`)
            }
            this.loadData()
            this.queryImportBatch();
          } else {
            this.$message.error(`${info.file.name} ${info.file.response.message}.`);
          }
        } else if (info.file.status === 'error') {
          this.$message.error(`文件上传失败: ${info.file.msg} `);
        }
      },
      //发送短信
      sendSms: function () {
        let batch = this.queryParam.importBatch;
        if(!batch){
          this.$message.warn("请选择要发送的批次！")
          return;
        }
        this.$refs.sendSmsModal.add();
        this.$refs.sendSmsModal.importBatch = batch;
        this.$refs.sendSmsModal.title = "发送短信";
        this.$refs.sendSmsModal.disableSubmit = false;
      },
      //查看激活
      graphReport: function () {
        this.$refs.graphReport.add();
        this.$refs.graphReport.title = "查看激活";
        this.$refs.graphReport.disableSubmit = false;
      },
      initDictConfig(){
      },
      queryImportBatch: function () {
        getAction('electronchannelorder/electronChannelOrder/queryImportBatch',null).then((res) => {
          if (res.success) {
           this.importBatch = res.result;
          }
        })
      }

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>