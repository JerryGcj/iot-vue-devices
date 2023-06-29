<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col v-has="'electonCardExtension:selectCustomerId'" :md="6" :sm="8">
<!--            <a-form-item label="客户" >-->
<!--              <j-tree-select-->
<!--                v-model="queryParam.cusId"-->
<!--                placeholder="请选择客户"-->
<!--                dict="sys_user,user_company,id"-->
<!--                condition='{"user_type":"4"}'-->
<!--                pidField="create_by_id"-->
<!--                pidValue="6469a4135f239e68058d92dc894d9935"/>-->
<!--            </a-form-item>-->
            <a-form-item label="创建人">
              <a-input placeholder="请输入创建人" v-model="queryParam.createBy"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="5">
            <a-form-item label="id">
              <a-input placeholder="请输入id" v-model="queryParam.id"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="订单状态">
              <j-dict-select-tag placeholder="请选择订单状态" v-model="queryParam.accountStatus" dict-code="account_status"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="强充状态">
              <j-dict-select-tag placeholder="请选择强充状态" v-model="queryParam.forceRecharge" dict-code="force_recharge"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="5">
            <a-form-item label="接入号">
              <a-input placeholder="请输入接入号" v-model="queryParam.accessNumber"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="5">
            <a-form-item label="账户余额">
              <a-input placeholder="请输入接入号" v-model="queryParam.accountBalance"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="5">
            <a-form-item label="订单号">
              <a-input placeholder="请输入订单号" v-model="queryParam.orderId"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="12">
            <a-form-item label="销售产品">
              <a-select
                v-model="operatorValue"
                allowClear="true"
                mode="multiple"
                style="width: 100%"
                placeholder="请选择"
                :options="dictOperatorOptions"
                :filterOption="likeQuery"
                :autoClearSearchValue="false"
                :maxTagTextLength=3
                :maxTagCount=3
              ></a-select>
            </a-form-item>
          </a-col>

          <a-col :md="8" :sm="12">
            <a-form-item label="运营商">
              <a-select
                v-model="operatorValueSimple"
                allowClear="true"
                mode="multiple"
                style="width: 100%"
                placeholder="请选择"
                :options="dictOperatorOptionsSimple"
                :filterOption="likeQuery"
                :autoClearSearchValue="false"
                :maxTagTextLength=3
                :maxTagCount=3
              ></a-select>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :md="8" :sm="10">
              <a-form-item label="激活日期">
                <j-date v-model="queryParam.activeTime_begin" date-format="YYYY-MM-DD 00:00:00" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
                <span style="width: 10px;"> ~ </span>
                <j-date v-model="queryParam.activeTime_end" date-format="YYYY-MM-DD 23:59:59" class="query-group-cust" placeholder="请选择结束时间"></j-date>
              </a-form-item>
            </a-col>
          </template>

          <a-col :md="8" :sm="8" >
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
      <!--<a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>-->
      <a-button type="primary" icon="download" @click="handleExportXls('electron_card_extension')" :disabled="isDisable">导出</a-button>
      <a-button type="primary" icon="import" v-has="'cardExtension:updateForceRecharge'" @click="handleImportXls">更新强充卡号</a-button>
      <!--<a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
        <!--<a-button type="primary" icon="import">导入</a-button>-->
      <!--</a-upload>-->
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <!--<a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>-->
      </a-dropdown>
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

        <span slot="accessNumber" slot-scope="accessNumber">
          <a v-text="accessNumber" @click="refreshStatus(accessNumber)"></a>
        </span>

        <span slot="orderId" slot-scope="orderId">
          <a v-text="orderId" @click="getOrder(orderId)"></a>
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

    <electronCardExtension-modal ref="modalForm" @ok="modalFormOk"></electronCardExtension-modal>
    <card-force-recharge-modal ref="importModal" @ok="modalFormOk"></card-force-recharge-modal>
    <order-modal ref="orderModal" @ok="modalFormOk"></order-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronCardExtensionModal from './modules/ElectronCardExtensionModal'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import JDate from '@/components/jeecg/JDate'
  import {downFile, getAction} from '@/api/manage'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import CardForceRechargeModal from "@views/iot/electronextension/modules/CardForceRechargeModal";
  import OrderModal from "../electronchannelorder/modules/OrderModal";
  export default {
    name: "ElectronCardExtensionList",
    mixins:[JeecgListMixin],
    components: {
      ElectronCardExtensionModal,
      JTreeSelect,
      JDate,
      JSearchSelectTag,
      CardForceRechargeModal,
      OrderModal
    },
    data () {
      return {
        isDisable: false,
        description: 'electron_card_extension管理页面',
        operatorValueSimple: [],
        dictOperatorOptionsSimple:[],

        operatorValue: [],
        dictOperatorOptions:[],
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
                title:'创建人',
                align:"center",
                dataIndex: 'createBy'
            },
          {
            title:'代理商',
            align:"center",
            dataIndex: 'cusId_dictText'
          },
          {
            title:'用户账号',
            align:"center",
            dataIndex: 'userName'
          },
            {
                title:'运营商',
                align:"center",
                dataIndex: 'agentId_dictText'
            },
          {
            title:'接入号',
            align:"center",
            dataIndex: 'accessNumber',
            scopedSlots: {customRender: 'accessNumber'}
          },
          {
            title:'下单手机号',
            align:"center",
            dataIndex: 'orderId_dictText',
          },
          {
            title:'订单号',
            align:"center",
            dataIndex: 'orderId',
            scopedSlots: {customRender: 'orderId'},
          },
          {
            title:'加包生效时间',
            align:"center",
            dataIndex: 'activeTime',
          },
          {
            title:'账户余额(分)',
            align:"center",
            dataIndex: 'accountBalance'
          },
          {
            title:'账户状态',
            align:"center",
            dataIndex: 'accountStatus_dictText'
          },
          {
            title:'强充状态',
            align:"center",
            dataIndex: 'forceRecharge_dictText'
          },
          {
            title:'停机原因',
            align:"center",
            dataIndex: 'stopReason',
          },
          {
            title:'插入时间',
            align:"center",
            dataIndex: 'createTime',
          },
          // {
          //   title: '操作',
          //   dataIndex: 'action',
          //   align:"center",
          //   scopedSlots: { customRender: 'action' }
          // }
        ],
        url: {
          list: "/electronextension/electronCardExtension/list",
          delete: "/electronextension/electronCardExtension/delete",
          deleteBatch: "/electronextension/electronCardExtension/deleteBatch",
          exportXlsUrl: "/electronextension/electronCardExtension/exportXls",
          importExcelUrl: "electronextension/electronCardExtension/importExcel",
          initOperatorUrl: "/electronchannelagent/electronChannelAgent/initOperator",
          initOperatorSimpleUrl:"/electronchannelagent/electronChannelAgent/initOperatorSimpleNameAll",
        },
        dictOptions:{
        },
      }
    },
      created () {
          this.operatorValue =[];
          this.operatorValueSimple =[];
          this.initOperator();
        this.initOperatorSimple();
      },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      initOperatorSimple(){
        getAction(this.url.initOperatorSimpleUrl).then((res)=>{
          if(res.success){
            this.dictOperatorOptionsSimple = res.result;
          }
        })
      },
      handleExportXls(fileName) {
        let param = {...this.queryParam};
        if (JSON.stringify(param) !== '{}'){
          this.isDisable = true;
          if(!fileName || typeof fileName != "string"){
            fileName = "导出文件"
          }
          if (this.selectedRowKeys && this.selectedRowKeys.length > 0) {
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
        } else {
          this.$message.error("请选择导出条件！")
        }
      },
        searchQuery(){
          if (this.operatorValue.length != 0){
            this.queryParam.agentId = this.operatorValue.join(",")
          }else {
            this.queryParam.agentId = this.operatorValueSimple.join(",")
          }
          this.loadData(1)
        },
        likeQuery(input, option) {
            return ( option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0 );
        },
        initOperator(){
            getAction(this.url.initOperatorUrl).then((res)=>{
                if(res.success){
                    this.dictOperatorOptions = res.result;
                }
            })

        },
      searchReset() {
        this.orderStatusValue = []
        this.channelNameValue = []
        this.operatorValue = []
        this.operatorValueSimple = []
        this.queryParam = {}
        this.loadData(1);
      },

      initDictConfig(){
      },
      refreshStatus:function(accessNumber){
        getAction('electronextension/electronCardExtension/refreshStatus',{accessNumber:accessNumber}).then((res) => {
          if (res.success) {
            this.$message.success(res.message);
            this.loadData();
          }else{
            this.$message.warning(res.message);
          }
        })

      },
      getOrder:function(orderId){
        getAction('electronregular/electronChannelOrderRegular/queryById',{id:orderId}).then((res) => {
          if (res.success) {
            this.$refs.orderModal.add(res.result);
            this.$refs.orderModal.disableSubmit = true;
          }else{
            this.$message.warning(res.message);
          }
        })

      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>