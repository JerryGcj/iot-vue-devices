<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6" >
            <a-form-item label="运营商">
              <j-dict-select-tag  v-model="queryParam.operationId" dict-code="electron_operator_config,operator,id"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="月份">
              <a-month-picker placeholder="请选择月份"  v-model="queryParam.month" />
            </a-form-item>
          </a-col>
            <a-col :md="4" :sm="4">
              <a-form-item label="默认数据">
                <a-select v-model="queryParam.defaultRecord" placeholder="请选择">
                  <a-select-option value="0">默认</a-select-option>
                  <a-select-option value="1">其他</a-select-option>
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

    <!-- 操作按钮区域 -->
    <div class="table-operator">
<!--      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>-->
      <a-button type="primary" icon="hdd" @click="calcOverall()">计算运营总体情况</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('运营总体报表')">导出</a-button>
<!--      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
<!--        <a-button type="primary" icon="import">导入</a-button>-->
<!--      </a-upload>-->
<!--      <a-dropdown v-if="selectedRowKeys.length > 0">-->
<!--        <a-menu slot="overlay">-->
<!--          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>-->
<!--        </a-menu>-->
<!--        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>-->
<!--      </a-dropdown>-->
    </div>

    <!-- table区域-begin -->
    <div>
<!--      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">-->
<!--        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项-->
<!--        <a style="margin-left: 24px" @click="onClearSelected">清空</a>-->
<!--      </div>-->

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

    <electronOperationOverall-modal ref="modalForm" @ok="modalFormOk"></electronOperationOverall-modal>
    <electron-operation-overall-calc ref="OverallCalc"  @ok="modalFormOk"></electron-operation-overall-calc>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ElectronOperationOverallModal from './modules/ElectronOperationOverallModal'
  import ElectronOperationOverallCalc from './modules/ElectronOperationOverallCalc'
  import JDate from '@/components/jeecg/JDate'
  import JDictSelectTag from "@comp/dict/JDictSelectTag";
  import { getAction } from '@api/manage'

  export default {
    name: "ElectronOperationOverallList",
    mixins: [JeecgListMixin],
    components: {
      ElectronOperationOverallModal,JDate,JDictSelectTag,ElectronOperationOverallCalc
    },
    data () {
      return {
        description: 'electron_operation_overall管理页面',
        // 表头
        columns: [
          {
            title: '#',
            align: 'center',
            dataIndex: 'rowIndex',
            customRender: function(text, r, index) {
              return (text !== '合计') ? (parseInt(index) + 1) : text
            }
          },
          {
            title:'月份',
            align:"center",
            dataIndex: 'month'
          },
          {
            title:'运营商',
            align:"center",
            dataIndex: 'operationId_dictText'
          },{
            title:'首月在网',
            align:"center",
            dataIndex: 'firstActive'
          },
          {
            title:'直营',
            align:"center",
            dataIndex: 'insideActive'
          },
          {
            title:'渠道',
            align:"center",
            dataIndex: 'externalActive'
          },
          {
            title:'最新留存',
            align:"center",
            dataIndex: 'retain'
          },
          {
            title:'预期总佣金',
            align:"center",
            dataIndex: 'expectCommission'
          },
          {
            title:'实际支出(推广费)',
            align:"center",
            dataIndex: 'realExpenses'
          },
          {
            title:'预期代理支出',
            align:"center",
            dataIndex: 'expectAgentExpenses'
          },
          {
            title:'预期总收入',
            align:"center",
            dataIndex: 'expectIncome'
          },
          {
            title:'实收佣金',
            align:"center",
            dataIndex: 'realInCommission'
          },
          {
            title:'未收佣金',
            align:"center",
            dataIndex: 'noInCommission'
          },
          {
            title:'平均在网时长(月)',
            align:"center",
            dataIndex: 'avgActiveMonth'
          },
          {
            title:'生成时间',
            align:"center",
            dataIndex: 'createTime',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
           },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        queryParam:{
          "defaultRecord":"0",
        },
        url: {
          list: "/electronoperationoverall/electronOperationOverall/list",
          delete: "/electronoperationoverall/electronOperationOverall/delete",
          deleteBatch: "/electronoperationoverall/electronOperationOverall/deleteBatch",
          exportXlsUrl: "/electronoperationoverall/electronOperationOverall/exportXls",
          importExcelUrl: "electronoperationoverall/electronOperationOverall/importExcel",
        },
        dictOptions:{
        },
        /* 分页参数 */
        ipagination: {
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        newArr: [],
        dataSource: [],
        newDataSource: []
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    mounted() {
      this.loadData();
    },
    watch: {
      'ipagination.pageSize': function(val) {
        this.dataHandling(val - 1)
      }
    },
    methods: {
      initDictConfig(){
      },
      calcOverall(){
        this.$refs.OverallCalc.add();
        this.$refs.OverallCalc.title = "计算运营成果";
        this.$refs.OverallCalc.disableSubmit = false;
      },
      loadData(arg) {

        if(!this.url.list){
          this.$message.error("请设置url.list属性!")
          return
        }
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件
        this.loading = true;
        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            this.dataSource = res.result.records;
            this.newDataSource = this.dataSource;
            this.ipagination.total = res.result.total;
            //console.log('this.ipagination.pageSize==',this.ipagination.pageSize)
            this.dataHandling(this.ipagination.pageSize - 1);
            //this.tableAddTotalRow(this.columns, this.dataSource);
          }
          if(res.code===510){
            this.$message.warning(res.message)
          }
          this.loading = false;
        })
      },
      /*如果分页走这个方法*/
      dataHandling(num) {
        this.newArr = [];
        let arrLength = this.newDataSource.length; // 数组长度;
        let index = 0;
        for (let i = 0; i < arrLength; i++) {
          if (i % num === 0 && i !== 0) {
            this.newArr.push(this.newDataSource.slice(index, i));
            index = i;
          }
          if ((i + 1) === arrLength) {
            this.newArr.push(this.newDataSource.slice(index, (i + 1)));
          }
        }
        var arrs = this.newArr;
        for (let i = 0; i < arrs.length; i++) {
          let arr = arrs[i];
          let newArr = {};
          newArr.month = "-";
          newArr.avgActiveMonth = "-";
          newArr.operationId_dictText = "-";
          newArr.createTime = "-";
          newArr.action = "-";
          newArr.rowIndex = "合计"
          var firstActive = 0;
          var insideActive = 0;
          var externalActive = 0;
          var retain = 0;
          var expectCommission = 0;
          var realExpenses = 0;
          var expectAgentExpenses = 0;
          var expectIncome = 0;
          var realInCommission = 0;
          var noInCommission = 0;
          for (let j = 0; j < arr.length; j++) {
            firstActive += arr[j].firstActive;
            insideActive += arr[j].insideActive;
            externalActive += arr[j].externalActive;
            retain += arr[j].retain;
            expectCommission += arr[j].expectCommission;
            realExpenses += arr[j].realExpenses;
            expectAgentExpenses += arr[j].expectAgentExpenses;
            expectIncome += arr[j].expectIncome;
            realInCommission += arr[j].realInCommission;
            noInCommission += arr[j].noInCommission;
          }
          newArr.firstActive = parseFloat(firstActive);
          newArr.insideActive = parseFloat(insideActive);
          newArr.externalActive = parseFloat(externalActive);
          newArr.retain = parseFloat(retain);
          newArr.expectCommission = parseFloat(expectCommission).toFixed(2);
          newArr.realExpenses = parseFloat(realExpenses).toFixed(2);
          newArr.expectAgentExpenses = parseFloat(expectAgentExpenses).toFixed(2);
          newArr.expectIncome = parseFloat(expectIncome).toFixed(2);
          newArr.realInCommission = parseFloat(realInCommission).toFixed(2);
          newArr.noInCommission = parseFloat(noInCommission).toFixed(2);
          arrs[i].push(newArr);
        }
        var newDataSource = [];
        for (let i = 0; i < arrs.length; i++) {
          let arr = arrs[i];
          for (var j in arr) {
            newDataSource.push(arr[j]);
          }
        }
        //console.log('前',this.dataSource)
        this.dataSource = Object.values(newDataSource);
        //console.log('后',this.dataSource)
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>