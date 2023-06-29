<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
           <!-- <a-form-item label="运营商">
              &lt;!&ndash;<a-input placeholder="请输入运营商" v-model="queryParam.operatorId"></a-input>&ndash;&gt;
                <a-select :dropdownStyle="{ maxHeight: '200px', overflow: 'auto' }"
                          v-model="queryParam.operatorId"
                          placeholder="运营商">
                  <a-select-option v-for="d in operatorData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
                </a-select>
            </a-form-item>-->
            <a-form-item label="运营商">
              <j-dict-select-tag v-model="queryParam.operatorId" placeholder="请选择运营商"
                                 dictCode="iot_operator,operator_name,id"/>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="客户">
              <!--<a-input placeholder="请输入客户" v-model="queryParam.userNameId"></a-input>-->
               <!--异步树加载组件 通过传入表名 显示字段 存储字段 加载一个树控件
               <j-tree-select dict="aa_tree_test,aad,id" pid-field="pid" ></j-tree-select>-->
              <j-tree-select
                v-model="queryParam.userId"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                condition='{"del_flag":"0"}'
                pidField="create_by_id"
                pidValue=""/>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="16">
            <a-form-item label="创建时间">
              <j-date :show-time="true" date-format="YYYY-MM-DD 00:00:00" placeholder="请选择开始时间" class="query-group-cust" v-model="queryParam.createTime_begin"></j-date>
              <span class="query-group-split-cust"></span>
              <j-date :show-time="true" date-format="YYYY-MM-DD 23:59:59" placeholder="请选择结束时间" class="query-group-cust" v-model="queryParam.createTime_end"></j-date>
            </a-form-item>
          </a-col>

            <a-col :md="6" :sm="8">
              <a-form-item label="合伙人">
                <!--<a-input placeholder="请输入合伙人" v-model="queryParam.partnerId"></a-input>-->
                <a-select :dropdownStyle="{ maxHeight: '200px', overflow: 'auto' }"
                          v-model="queryParam.partnerId"
                          placeholder="合伙人">
                  <a-select-option v-for="d in partnerData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="订单号">
                <a-input placeholder="请输入订单号" v-model="queryParam.orderId"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="子订单号">
                <a-input placeholder="请输入子订单号" v-model="queryParam.sonOrderId"></a-input>
              </a-form-item>
            </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :md="6" :sm="8">
              <a-form-item label="ICCID">
                <a-input placeholder="请输入ICCID" v-model="queryParam.iccid"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="MSISDN">
                <a-input placeholder="请输入MSISDN" v-model="queryParam.msisdn"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="套餐编号">
                <a-input placeholder="请输入套餐编号" v-model="queryParam.setmealId"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="套餐">
                <a-input placeholder="请输入套餐" v-model="queryParam.setmealName"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="状态">
                <a-select v-model="queryParam.status" placeholder="请选择状态类型">
                  <a-select-option value="0">创建</a-select-option>
                  <a-select-option value="1">处理中</a-select-option>
                  <a-select-option value="2">成功</a-select-option>
                  <a-select-option value="3">失败</a-select-option>
                  <a-select-option value="4">待确认</a-select-option>
                  <a-select-option value="5">下个月续费</a-select-option>
                  <a-select-option value="6">已提交</a-select-option>
                  <a-select-option value="7">待回调</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="生效方式">
                <a-select v-model="queryParam.sffectiveway" placeholder="请选择状态类型">
                  <a-select-option value="0">立刻生效</a-select-option>
                  <a-select-option value="1">下月生效</a-select-option>
                  <a-select-option value="2">到期生效</a-select-option>
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
      <a-button type="primary" icon="download" @click="handleExportXls('子订单列表')">导出</a-button>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <!--<a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>-->
          <a-menu-item key="1" @click="handlesonorders('2')"><a-icon type="check-circle"/>成功</a-menu-item>
          <a-menu-item key="2" @click="handlesonorders('3')"><a-icon type="close-circle"/>失败</a-menu-item>
          <!--<a-menu-item key="3" @click="handlesonorders('4')"><a-icon type="pay-circle"/>重新退款</a-menu-item>-->
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
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
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        
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
          <a @click="handleDetail(record)">详情</a>
          <a-popconfirm title="确定失败吗?" @confirm="() => handleorderfail(record.id)">
            <a class="ant-dropdown-link" v-if="record.status=='0'" ><a-divider type="vertical" />失败</a>
            <a class="ant-dropdown-link" v-else-if="record.status=='1'"><a-divider type="vertical" />失败</a>
            <a class="ant-dropdown-link" v-else-if="record.status=='4'"><a-divider type="vertical" />失败</a>
            <a class="ant-dropdown-link" v-else-if="record.status=='5'"><a-divider type="vertical" />失败</a>
            <a class="ant-dropdown-link" v-else-if="record.status=='6'"><a-divider type="vertical" />失败</a>
            <a class="ant-dropdown-link" v-else-if="record.status=='7'"><a-divider type="vertical" />失败</a>
          </a-popconfirm>
          <!--<a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                  <a @click="handleordersuccess(record.id)">成功</a>
              </a-menu-item>
              <a-menu-item>
                  <a @click="handleordersuccess(record.id)">失败</a>
              </a-menu-item>
              <a-menu-item>
                  <a @click="handleordersuccess(record.id)">重新退款</a>
              </a-menu-item>
            </a-menu>
          </a-dropdown>-->
        </span>

      </a-table>
    </div>

    <iotSonOrder-modal ref="modalForm" @ok="modalFormOk"></iotSonOrder-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotSonOrderModal from './modules/IotSonOrderModal'
  import JDate from '@/components/jeecg/JDate.vue'
  import {queryOperator,queryPartner } from '@/api/api'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import {putAction} from '@/api/manage'
  export default {
    name: "IotSonOrderList",
    mixins:[JeecgListMixin],
    components: {
      JDate,
      IotSonOrderModal,
      queryOperator,
      queryPartner,
      JTreeSelect
    },
    data () {
      return {
        description: 'iot_son_order管理页面',
        operatorData:[],
        partnerData:[],
        formData: {},
        // 表头
        columns: [
          {
            title:'子订单号',
            align:"center",
            dataIndex: 'sonOrderId'
          },
          {
            title:'ICCID',
            align:"center",
            dataIndex: 'iccid'
          },
          {
            title:'MSISDN',
            align:"center",
            dataIndex: 'msisdn'
          },
          {
            title:'企业',
            align:"center",
            dataIndex: 'userName'
          },
          {
            title:'套餐',
            align:"center",
            dataIndex: 'setmealName'
          },
          {
            title:'交易金额（元）',
            align:"center",
            dataIndex: 'sellPrice'
          },
          {
            title:'购买数量',
            align:"center",
            dataIndex: 'buyNum'
          },
          {
            title:'状态',
            align:"center",
            dataIndex: 'status',
            customRender:function (text) {
              if(text=='0'){
                return "创建";
              }else if(text=="1"){
                return "处理中";
              }else if(text=="2"){
                return "成功";
              }else if(text=="3"){
                return "失败";
              }else if(text=="4"){
                return "未确认";
              }else if(text=="5"){
                return "下个月续费";
              }else if(text=="6"){
                return "已提交";
              }else if(text=="7"){
                return "待回调";
              } else {
                return text;
              }
            }
          },
          {
            title:'充值摘要',
            align:"center",
            dataIndex: 'msg'
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
          list: "/iotsonorder/iotSonOrder/list",
          delete: "/iotsonorder/iotSonOrder/delete",
          deleteBatch: "/iotsonorder/iotSonOrder/deleteBatch",
          exportXlsUrl: "/iotsonorder/iotSonOrder/exportXls",
          importExcelUrl: "iotsonorder/iotSonOrder/importExcel",
          updateSonOrderStatusFailUrl:"/iotsonorder/iotSonOrder/updateSonOrderStatusFail",
          updateSonOrdersStatusUrl:"/iotsonorder/iotSonOrder/updateSonOrdersStatus"
        },
        dictOptions:{
        },
        queryParam: {
          createTime_begin: this.dateFormat(new Date()),
          createTime_end: this.dateFormat(new Date())
        }
      }
    },
    //加载页面时触发的 查询运营商方法
    mounted:function(){
      this.createcodeQueryOperator();//查询运营商方法
      this.createcodeQueryPartner();//查询合伙人方法
      /*this.tableAddTotalRow();*/
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },

    methods: {
      createcodeQueryOperator(){
        var that = this;
        queryOperator().then((res)=>{
          if(res.success){
            that.operatorData = [];
            let treeList = res.result
            for(let a=0;a<treeList.length;a++){
              let temp = treeList[a];
              this.operatorData.push({
                value:temp.id,
                text:temp.operatorName
              })
            }
          }
        });
      },
      createcodeQueryPartner(){
        var that = this;
        queryPartner().then((res)=>{
          console.log(res.success);
          if(res.success){
            that.partnerData = [];
            let treeList = res.result
            for(let a=0;a<treeList.length;a++){
              let temp = treeList[a];
              this.partnerData.push({
                value:temp.id,
                text:temp.userCompany
              })
            }
          }
        });
      },
      initDictConfig(){
      },
      handleorderfail(record) {
        let params = {id: record}
        //初始化客户详情
        putAction(this.url.updateSonOrderStatusFailUrl, params).then((res) => {
          if (res.success) {
            this.$message.success(res.message);
            this.loadData();
          }else{
            this.$message.warning(res.message);
          }
        });
      },
      handlesonorders: function (status) {
        if (this.selectedRowKeys.length <= 0) {
          this.$message.warning('请选择一条记录！');
          return;
        } else {
          let ids = "";
          let that = this;
          that.selectedRowKeys.forEach(function (val) {
            ids += val + ",";
          });
          this.$confirm({
            title: "确认操作",
            content: "是否" + (status == '2' ? "成功" : "失败") + "选中订单?",
            onOk: function () {
              let params = {ids: ids,status: status}
              putAction(that.url.updateSonOrdersStatusUrl, params).then((res) => {
                if (res.success) {
                  that.$message.success(res.message);
                  that.loadData();
                  that.onClearSelected();
                }else{
                  that.$message.warning(res.message);
                }
              });
            }
          });
        }
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>