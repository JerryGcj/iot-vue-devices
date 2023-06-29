<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col v-has="'orderList:selectCustomerId'" :md="6" :sm="8">
            <a-form-item label="客户" >
              <j-tree-select
                v-model="queryParam.userId"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                condition='{"del_flag":"0"}'
                pidField="create_by_id"
                pidValue=""/>
            </a-form-item>
          </a-col>

          <a-col v-has="'orderList:selectCustomerIdDls'" :md="6" :sm="8">
            <a-form-item label="客户">
              <a-select v-model="queryParam.userId"
                        placeholder="请选择客户">
                <a-select-option v-for="d in userData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item label="时间">
              <j-date placeholder="请选择开始日期" class="query-group-cust" v-model="queryParam.createTime_begin" dateFormat="YYYY-MM-DD 00:00:00"></j-date>
              <span class="query-group-split-cust"></span>
              <j-date placeholder="请选择结束日期" class="query-group-cust" v-model="queryParam.createTime_end" dateFormat="YYYY-MM-DD 23:59:59"></j-date>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="提现状态">
              <a-select v-model="queryParam.auditStatus" placeholder="请选择提现状态">
                <a-select-option value="0">待审核</a-select-option>
                <a-select-option value="1">待打款</a-select-option>
                <a-select-option value="2">驳回</a-select-option>
                <a-select-option value="3">已打款</a-select-option>
                <a-select-option value="4">提现异常</a-select-option>
                <a-select-option value="5">提现失败</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <!--<a-col :md="6" :sm="8">-->
            <!--<a-form-item label="提现单号">-->
              <!--<a-input placeholder="请输入提现单号" v-model="queryParam.orderNo"></a-input>-->
            <!--</a-form-item>-->
          <!--</a-col>-->
          <a-col :md="6" :sm="8">
            <a-form-item label="申请人">
              <a-input placeholder="请输入申请人" v-model="queryParam.userName"></a-input>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :md="6" :sm="8">
              <a-form-item label="审核人">
                <a-input placeholder="请输入审核人" v-model="queryParam.updateUser"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="提现方式">
                <a-select v-model="queryParam.withdrawalWay" placeholder="请选择提现方式">
                  <a-select-option value="0">银行</a-select-option>
                  <a-select-option value="1">微信</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
          </template>
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
        <!--<a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>-->
        <!--<a-button type="primary" icon="download" @click="handleExportXls('提现单')">导出</a-button>-->
      </div>

    <!-- table区域-begin -->
    <div>
      <!--<div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>-->

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
        <template slot="auditStatus" slot-scope="status">
          <a-tag v-if="status==0" color="gray">待审核</a-tag>
          <a-tag v-if="status==1" color="cyan">待打款</a-tag>
          <a-tag v-if="status==2" color="red">驳回</a-tag>
          <a-tag v-if="status==3" color="green">已打款</a-tag>
          <a-tag v-if="status==4" color="purple">提现异常</a-tag>
          <a-tag v-if="status==5" color="red">提现失败</a-tag>
        </template>
        <span slot="action" slot-scope="text, record">
          <a v-if="record.auditStatus!='3'" @click="handleAudit(record)">审核<a-divider type="vertical" /></a>
          <!--<a @click="handleAudit(record)">审核</a>-->
          <a @click="shareProfits(record)">分润单详情</a>

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
    </div>
    <Select-user-modal ref="selectUserModal" @ok="modalFormOk"></Select-user-modal>
    <iotWithdrawDeposit-modal ref="modalForm" @ok="modalFormOk"></iotWithdrawDeposit-modal>
    <modal-audit-form ref="modalAuditForm" @ok="modalFormOk"></modal-audit-form>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotWithdrawDepositModal from './modules/IotWithdrawDepositModal'
  import SelectUserModal from './modules/SelectUserModal'
  import modalAuditForm from './modules/IotWithdrawDepositAuditModal'
  import JDictSelectTag from '@/components/dict/JDictSelectTag.vue'
  import JDate from '@/components/jeecg/JDate.vue'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import {queryLowerAgent} from '@/api/api'

  export default {
    name: "IotWithdrawDepositList",
    mixins:[JeecgListMixin],
    components: {
      JDictSelectTag,
      JDate,
      IotWithdrawDepositModal,
      JTreeSelect,
      modalAuditForm,
      SelectUserModal
    },
    data () {
      return {
        description: '提现单管理页面',
        userData:[],
        // 表头
        columns: [
          /*{
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },*/
          // {
          //   title:'提现单号',
          //   align:"center",
          //   dataIndex: 'orderNo'
          // },
          {
            title:'申请人',
            align:"center",
            dataIndex: 'userName'
          },
          {
            title:'代理商',
            align:"center",
            dataIndex: 'realName'
          },
          /*{
            title:'店铺',
            align:"center",
            dataIndex: 'userCompany'
          },*/
          {
            title:'提现金额(元)',
            align:"center",
            dataIndex: 'money'
          },
          {
            title:'提现方式',
            align:"center",
            dataIndex: 'withdrawalWay',
            customRender:(text)=>{
              if(text=='0'){
                return "银行";
              }else if(text=='1'){
                return "微信";
              }else {
                return text;
              }
            }
          },
          {
            title:'审核人',
            align:"center",
            dataIndex: 'updateUser'
          },
          /*{
            title:'付款账户',
            align:"center",
            dataIndex: 'paymentUser'
          },*/
          {
            title:'审核备注',
            align:"center",
            dataIndex: 'auditRemark'
          },
          {
            title:'状态',
            align:"center",
            dataIndex: 'auditStatus',
            scopedSlots: { customRender: 'auditStatus' }
          },
          {
            title:'提现申请信息',
            align:"center",
            dataIndex: 'returnMsg',
          },
          {
            title:'申请时间',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/withdrawdeposit/iotWithdrawDeposit/list",
          delete: "/withdrawdeposit/iotWithdrawDeposit/delete",
          deleteBatch: "/withdrawdeposit/iotWithdrawDeposit/deleteBatch",
          exportXlsUrl: "/withdrawdeposit/iotWithdrawDeposit/exportXls",
          importExcelUrl: "withdrawdeposit/iotWithdrawDeposit/importExcel",
        },
        dictOptions:{
         withdrawalType:[],
         auditStatus:[],
        },
        queryParam: {
          createTime_begin: this.dateFormat(new Date()),
          createTime_end: this.dateFormat(new Date())
        }
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    mounted() {
      var that = this;
      queryLowerAgent().then((res)=>{
        if(res.success){
          that.userData = [];
          let treeList = res.result
          for(let a=0;a<treeList.length;a++){
            let temp = treeList[a];
            this.userData.push({
              value:temp.id,
              text:temp.userCompany
            })
          }
        }
      });
    },
    methods: {
      shareProfits:function(record){
        this.$refs.selectUserModal.edit(record);
        this.$refs.selectUserModal.disableSubmit = true;
      },
      handleAudit: function (record) {
        this.$refs.modalAuditForm.edit(record);
        this.$refs.modalAuditForm.title = "审核";
        this.$refs.modalAuditForm.disableSubmit = false;
      },
      initDictConfig(){
        initDictOptions('').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'withdrawalType', res.result)
          }
        })
        initDictOptions('').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'auditStatus', res.result)
          }
        })
      }

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>