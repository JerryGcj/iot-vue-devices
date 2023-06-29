<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
<!--          <a-col v-has="'electronShareProfit:selectCustomerId'" :md="6" :sm="8">-->
          <a-col :md="6" :sm="8">
          <a-form-item label="销售代表" >
            <a-select
              placeholder="请选择销售代表"
              v-model="queryParam.higherAgentId"
              allow-clear
            >
              <a-select-option v-for="(sale,saleIndex) in salesList" :key="saleIndex.toString()" :value="sale.value">
                {{ sale.label }}
              </a-select-option>
            </a-select>
          </a-form-item>
<!--              <j-tree-select-->
<!--                v-model="queryParam.higherAgentId"-->
<!--                placeholder="请选择客户"-->
<!--                dict="sys_user,user_company,id"-->
<!--                condition='{"del_flag":"0"}'-->
<!--                pidField="create_by_id"-->
<!--                pidValue=""/>-->
<!--            </a-form-item>-->
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="代理商" >
              <a-input
                placeholder="请输入代理商"
                v-model="queryParam.higherAgent"
                allow-clear
              >
              </a-input>
            </a-form-item>
            <!--              <j-tree-select-->
            <!--                v-model="queryParam.higherAgentId"-->
            <!--                placeholder="请选择客户"-->
            <!--                dict="sys_user,user_company,id"-->
            <!--                condition='{"del_flag":"0"}'-->
            <!--                pidField="create_by_id"-->
            <!--                pidValue=""/>-->
            <!--            </a-form-item>-->
          </a-col>
<!--          <a-col v-has="'electronShareProfit:selectCustomerIdDls'" :md="6" :sm="8">-->
<!--            <a-form-item label="客户">-->
<!--              <j-tree-select-->
<!--                v-model="queryParam.higherAgentId"-->
<!--                placeholder="请选择客户"-->
<!--                dict="sys_user,realname,id"-->
<!--                condition='{"del_flag":"0"}'-->
<!--                pidField="create_by_id"-->
<!--                pidValue="6469a4135f239e68058d92dc894d9935"/>-->
<!--            </a-form-item>-->
<!--          </a-col>-->
          <a-col :md="6" :sm="8">
            <a-form-item label="运营商">
<!--              <a-select-->
<!--                v-model="operatorValue"-->
<!--                mode="multiple"-->
<!--                style="width: 100%"-->
<!--                placeholder="请选择"-->
<!--                :options="dictOperatorOptions"-->
<!--                :filterOption="likeQuery"-->
<!--                :autoClearSearchValue="false"-->
<!--                :maxTagTextLength=3-->
<!--                :maxTagCount=3-->
<!--              ></a-select>-->
              <a-tree-select
                v-model="queryParam.agentId"
                style="width: 100%"
                :dropdown-style="{ maxHeight: '400px', overflow: 'auto' }"
                placeholder="请选择运营商"
                allow-clear
                :tree-data="treeData"
              />
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="6">
            <a-form-item label="分润月份">
              <!--<j-date placeholder="请选择分润月份" class="query-group-cust" v-model="queryParam.createTimeBegin" dateFormat="YYYY-MM-DD 00:00:00"></j-date>
              <span class="query-group-split-cust"></span>
              <j-date placeholder="请选择结束日期" class="query-group-cust" v-model="queryParam.createTimeEnd" dateFormat="YYYY-MM-DD 23:59:59"></j-date>-->
              <a-month-picker placeholder="请选择月份" @change="onChange" />
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button type="primary" icon="download" @click="handleExportXls('分润明细')" style="margin-left: 8px">导出</a-button>
              <a-button type="primary" v-has="'electronShareProfit:shareCount'" icon="money-collect" @click="shareTotal" style="margin-left: 8px">分润合计</a-button>
              <!--<a-button type="primary" v-has="'IotShareProfitsList:handleRefund'" icon="reload" @click="handleRefund" style="margin-left: 8px">退款</a-button>-->
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

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
        <span slot="action" slot-scope="text, record">
          <!--<a @click="handleDetail(record)">详情</a>-->
          <a @click="handleDetails(record)">详情</a>
        </span>
        <!--<template slot="footer" slot-scope="currentPageData">
          合计:{{summoney}}
        </template>-->
      </a-table>
    </div>

    <electronShareProfits-modal ref="modalForm" @ok="modalFormOk"></electronShareProfits-modal>
    <share-profits-modal ref="shareProfitsModal" @ok="modalFormOk"></share-profits-modal>
    <Select-user-modal ref="selectUserModal" @ok="modalFormOk"></Select-user-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronShareProfitsModal from './modules/ElectronShareProfitsModal'
  import { getAction,httpAction } from '@/api/manage'
  import {queryLowerAgent} from '@/api/api'
  import JDate from '@/components/jeecg/JDate.vue'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import SelectUserModal from './modules/SelectUserModal'
  import ShareProfitsModal from './modules/ShareProfitsModal'

  export default {
    name: "ElectronShareProfitsList",
    mixins:[JeecgListMixin],
    components: {
      ElectronShareProfitsModal,
      queryLowerAgent,
      JDate,
      JTreeSelect,
      SelectUserModal,
      ShareProfitsModal
    },
    data () {
      return {
        salesList:[],
        treeData: [],
        description: 'electron_share_profits管理页面',
        operatorValue: [],
        dictOperatorOptions:[],
        formData: {},
        userData: [],
        // 表头
        columns: [
          {
            title: '#',
            width: '180px',
            align: 'center',
            dataIndex: 'rowIndex',
            customRender: function (text, r, index) {
              return (text !== '合计') ? (parseInt(index) + 1) : text
            }
          },
          {
            title:'代理商',
            align:"center",
            dataIndex: 'agentName'
          },
          {
            title:'运营商',
            align:"center",
            dataIndex: 'channelAgentName'
          },
          /*{
            title:'运营商',
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
          },*/
          {
            title:'总分润金额(元)',
            align:"center",
            dataIndex: 'shareMoney'
          },
          {
            title:'已分润金额(元)',
            align:"center",
            dataIndex: 'hasShare'
          },
          {
            title:'未分润金额(元)',
            align:"center",
            dataIndex: 'noShare'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/electronshareprofits/electronShareProfits/list",
          delete: "/electronshareprofits/electronShareProfits/delete",
          deleteBatch: "/electronshareprofits/electronShareProfits/deleteBatch",
          exportXlsUrl: "/electronshareprofits/electronShareProfits/exportXls",
          importExcelUrl: "electronshareprofits/electronShareProfits/importExcel",
          initOperatorUrl: "/electronchannelagent/electronChannelAgent/initOperator",
          initOperatorTree: "/electronchannelagent/electronChannelAgent/initOperatorTree",
        },
        dictOptions:{
          higherAgent:[],
          lowerAgent:[],
          status:[]
        },
        queryParam:{
          createTime:''
        }
      }
    },
    created () {
       this.operatorValue =[];
       this.initOperator();
       this.getInitOperatorTree();
       this.initSalesList();
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
      // getAction(this.url.list,{}).then(res => {
      //   if(res.success){
      //     console.log("chouchou"+JSON.stringify(res.result.records));
      //     this.tableAddTotalRow(this.columns, res.result.records)
      //   }
      // });
    },
    methods: {
      initSalesList(){
        getAction('/sys/user/initSalesList?roleId=1565584782662606850',null).then((res)=>{
          if(res.success){
            this.salesList = res.result;
          }
        })
      },
      getInitOperatorTree() {
        getAction(this.url.initOperatorTree).then((res)=>{
          if(res.success){
            this.treeData = res.result;
          }
        })
        console.log(this.treeData)
      },
        searchQuery(){
            // this.queryParam.agentId = this.operatorValue.join(",")
            getAction(this.url.list,this.queryParam).then(res=>{
                if (res.success) {
                    this.dataSource = res.result.records;
                }
            })
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
      initDictConfig(){
        initDictOptions('').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'higherAgent', res.result)
          }
        })
        initDictOptions('').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'lowerAgent', res.result)
          }
        })
        initDictOptions('').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'status', res.result)
          }
        })
      },
      onChange(date, dateString) {
        this.queryParam.createTime=dateString;
      },
      handleDetails(record){
        this.$refs.selectUserModal.edit(record);
        this.$refs.selectUserModal.disableSubmit = true;
      },
      shareTotal(){
        const that = this;
        var higherAgentId = this.queryParam.higherAgentId;
        var time = this.queryParam.createTime;
        //var timeEnd = this.queryParam.createTimeEnd;
        if(!higherAgentId){
          that.$message.warning('请选择结算客户');
          return false;
        }
        this.$refs.shareProfitsModal.edit(higherAgentId,time);
        this.$refs.shareProfitsModal.disableSubmit = true;

      },
      /** 表格增加合计行 */
      tableAddTotalRow(columns, dataSource) {

        console.log("6511651"+dataSource);
        let numKey = 'rowIndex';
        let totalRow = { [numKey]: '合计' };
        columns.forEach(column => {
          let { key, dataIndex } = column;
          if (![key, dataIndex].includes(numKey)) {

            let total = 0;
            dataSource.forEach(data => {
              total += /^(([1-9]{1}\d*)|(0{1}))(\.\d{1,2})?$/.test(data[dataIndex]) ? Number.parseFloat(data[dataIndex]) : Number.NaN
              console.log(data[dataIndex], ':', (/^^(([1-9]{1}\d*)|(0{1}))(\.\d{1,2})?$/.test(data[dataIndex]) ? Number.parseFloat(data[dataIndex]) : Number.NaN))
            });

            if (Number.isNaN(total)) {
              total = '-'
            }
            totalRow[dataIndex] = total
          }
        })

        this.dataSource.push(totalRow)
      },
      handleRefund: function () {
        this.$refs.handleRefund.add();
        this.$refs.handleRefund.title = "退款";
        this.$refs.handleRefund.disableSubmit = false;
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>