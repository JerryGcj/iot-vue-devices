<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col v-has="'orderList:selectCustomerId'" :md="4" :sm="6">
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

          <a-col v-has="'orderList:selectCustomerIdDls'" :md="4" :sm="6">
            <a-form-item label="客户">
              <a-select v-model="queryParam.userId"
                        placeholder="请选择客户">
                <a-select-option v-for="d in userData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="卡号">
              <a-input placeholder="请输入卡号" v-model="queryParam.iccid"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="手机号">
              <a-input placeholder="请输入手机号" v-model="queryParam.mobile"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="6">
            <a-form-item label="openid">
              <a-input placeholder="请输入openid" v-model="queryParam.openId"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="运营商">
              <a-select v-model="queryParam.operatorType" placeholder="请选择运营商">
                <a-select-option value="1">移动</a-select-option>
                <a-select-option value="2">联通</a-select-option>
                <a-select-option value="3">电信</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>

            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('iot_card_wechat_relation')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div> -->

    <!-- table区域-begin -->
    <div>


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

        <template slot="autoRenewal" slot-scope="text, record, index">
          <a-switch checkedChildren="是" unCheckedChildren="否" v-model="record.autoRenewal" @change="autoChange" @click="autoSwitch(record.id)" />
        </template>
        <template slot="refundSwitch" slot-scope="text, record, index">
          <a-switch checkedChildren="打开" unCheckedChildren="关闭" v-model="record.refundSwitch" @change="refundChange" @click="refundSwitch(record)" />
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

        <span slot="iccid" slot-scope="iccid, record">
          <a v-text="iccid" @click="showOrder(record)"></a>
        </span>
        <span slot="openId" slot-scope="openId">
          <a v-text="openId" @click="showDetail(openId)"></a>
        </span>
        <span slot="account" slot-scope="account, record">
          <a v-text="account" @click="showConsumer(record)"></a>
        </span>
        <span slot="action" slot-scope="text, record">
          <a-popconfirm v-if="record.delFlag==0" title="确定解绑吗?" @confirm="() => handleDelete(record.id)">
            <a v-has="'wechat:handleDelete'">解绑</a>
          </a-popconfirm>
          <a-popconfirm v-if="record.account>0" title="确定清零吗?" @confirm="() => clearBalance(record.id)">
            <a v-has="'wechat:clearBalance'"><a-divider type="vertical" />余额清零</a>
          </a-popconfirm>

          <!--<a @click="handleEdit(record)">编辑</a>-->

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a v-has="'wechat:changeCard'" @click="changeCard(record)">换卡</a>
              </a-menu-item>
              <a-menu-item>
                <a v-has="'wechat:transferAccount'" @click="transferAccount(record)">余额转移</a>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <iotCardWechatRelation-modal ref="modalForm" @ok="modalFormOk"></iotCardWechatRelation-modal>
    <recharge-order-modal ref="rechargeOrderModal" @ok="modalFormOk"></recharge-order-modal>
    <order-modal ref="orderModal" @ok="modalFormOk"></order-modal>
    <card-order-modal ref="cardOrderModal" @ok="modalFormOk"></card-order-modal>
    <consumer-modal ref="consumerModal" @ok="modalFormOk"></consumer-modal>
    <change-card-modal ref="changeCardModal" @ok="modalFormOk"></change-card-modal>
    <transfer-account-modal ref="transferAccountModal" @ok="modalFormOk"></transfer-account-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotCardWechatRelationModal from './modules/IotCardWechatRelationModal'
  import {queryLowerAgent} from '@/api/api'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import {putAction} from '@/api/manage';
  import RechargeOrderModal from './modules/RechargeOrderModal'
  import OrderModal from './modules/OrderModal'
  import CardOrderModal from "./modules/CardOrderModal";
  import ConsumerModal from "./modules/ConsumerModal";
  import ChangeCardModal from "./modules/ChangeCardModal";
  import TransferAccountModal from "./modules/TransferAccountModal";

  export default {
    name: "IotCardWechatRelationList",
    mixins:[JeecgListMixin],
    components: {
      IotCardWechatRelationModal,
      JTreeSelect,
      RechargeOrderModal,
      OrderModal,
      CardOrderModal,
      ConsumerModal,
      ChangeCardModal,
      TransferAccountModal
    },
    data () {
      return {
        description: '终端用户管理页面',
        userData:[],
        autoChecked: "",
        refundChecked: "",
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
            title:'iccid',
            align:"center",
            dataIndex: 'iccid',
            scopedSlots: {customRender: 'iccid'}
          },
          {
            title:'自定义卡号',
            align:"center",
            dataIndex: 'virtualIccid'
          },
          {
            title: '运营商',
            align:"center",
            dataIndex: 'operatorType',
            customRender:function (text) {
              if(text=='1'){
                return "移动";
              }else if(text=="2"){
                return "联通";
              }else if(text=="3"){
                return "电信";
              } else {
                return text;
              }
            }
          },
          {
            title:'openid',
            align:"center",
            dataIndex: 'openId',
            scopedSlots: {customRender: 'openId'}
          },
          {
            title:'用户号码',
            align:"center",
            dataIndex: 'mobile'
          },
          // {
          //   title:'用户类型',
          //   align:"center",
          //   dataIndex: 'userType',
          //   customRender:(text)=>{
          //     if(text=='0'){
          //       return "终端用户";
          //     }else {
          //       return text;
          //     }
          //   }
          // },
          {
            title:'用户类型',
            align:"center",
            dataIndex: 'isNew',
            customRender:(text)=>{
              if(text=='0'){
                return "新用户";
              }else {
                return "老用户";
              }
            }
          },
          {
            title:'状态',
            align:"center",
            dataIndex: 'status',
            customRender:(text)=>{
              if(text=='0'){
                return "正常";
              }else {
                return text;
              }
            }
          },
          {
            title:'企业名称',
            align:"center",
            dataIndex: 'userComapny'
          },
          {
            title:'余额(元)',
            align:"center",
            dataIndex: 'account',
            scopedSlots: {customRender: 'account'}
          },
          {
            title: '退款开关',
            align: "center",
            width: 80,
            scopedSlots: { customRender: 'refundSwitch' }
          },
          {
            title:'创建时间',
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
          list: "/wechatpetname/iotCardWechatRelation/list",
          delete: "/wechatpetname/iotCardWechatRelation/delete",
          deleteBatch: "/wechatpetname/iotCardWechatRelation/deleteBatch",
          exportXlsUrl: "/wechatpetname/iotCardWechatRelation/exportXls",
          importExcelUrl: "wechatpetname/iotCardWechatRelation/importExcel",
          autoRenewal: "/wechatpetname/iotCardWechatRelation/autoRenewal",
          clearBalanceUrl: "wechatpetname/iotCardWechatRelation/clearBalance",
          refundSwitch: "wechatpetname/iotCardWechatRelation/refundSwitch"
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
    created() {
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
      initDictConfig(){
      },
      autoChange(checked) {
        this.autoChecked = checked;
      },
      autoSwitch(id){
        const that = this;
        putAction(this.url.autoRenewal,{autoChecked: this.autoChecked,id: id}).then((res)=>{
          if(res.success){
            that.$message.success(res.message);
            that.$emit('ok');
            this.initData();
          }else{
            that.$message.warning(res.message);
          }
        });
      },
      clearBalance(id){
        const that = this;
        putAction(this.url.clearBalanceUrl,{id: id}).then((res)=>{
          if(res.success){
            that.$message.success(res.message);
            that.$emit('ok');
            that.loadData();
          }else{
            that.$message.warning(res.message);
          }
        });
      },
      showDetail:function(openid){
        this.$refs.rechargeOrderModal.edit(openid);
        this.$refs.rechargeOrderModal.title = "预充订单";
        this.$refs.rechargeOrderModal.disableSubmit = true;
      },
      showConsumer:function(record){
        this.$refs.consumerModal.edit(record.openId);
        this.$refs.consumerModal.title = "消费流水";
        this.$refs.consumerModal.disableSubmit = true;
      },
      showOrder:function(record){
        if(record.operatorType=="1"){
          this.$refs.orderModal.edit(record.iccid);
          this.$refs.orderModal.title = "订单";
          this.$refs.orderModal.disableSubmit = true;
        }
        if(record.operatorType=="2"){
          this.$refs.cardOrderModal.edit(record.iccid);
          this.$refs.cardOrderModal.title = "订单";
          this.$refs.cardOrderModal.disableSubmit = true;
        }
      },
      changeCard(record){
        this.$refs.changeCardModal.edit(record);
        this.$refs.changeCardModal.title = "换卡";
        this.$refs.changeCardModal.disableSubmit = false;
      },
      transferAccount(record){
        this.$refs.transferAccountModal.edit(record);
        this.$refs.transferAccountModal.title = "余额转移";
        this.$refs.transferAccountModal.disableSubmit = false;
      },
      refundChange(checked) {
        this.refundChecked = checked;
      },
      refundSwitch(record){
        const that = this;
        putAction(this.url.refundSwitch,{refundChecked: this.refundChecked,id: record.id}).then((res)=>{
          if(res.success){
            that.$message.success(res.message);
            that.$emit('ok');
            that.loadData();
          }else{
            that.$message.warning(res.message);
          }
        });
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>