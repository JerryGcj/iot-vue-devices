<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="奖励名称">
              <j-input placeholder="请输入分成奖励名称" v-model="queryParam.rewardsName"></j-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="运营商">
              <a-select v-model="queryParam.operatorName" placeholder="请选择运营商" :allowClear="true" @change="operatorChange">
                <a-select-option value="unicom">联通</a-select-option>
                <a-select-option value="mobile">移动</a-select-option>
                <a-select-option value="telecom">电信</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="归属省市">
              <a-select v-model="queryParam.belongArea" placeholder="请选择归属省市" ref="ad" :allowClear="true">
                <a-select-option v-for="(item,index) in belongAreaList" :key="index" :value="item">{{
                    item
                  }}
                </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="创建人">
              <a-select v-model="queryParam.createBy" placeholder="请选择创建人" allowClear>
                <a-select-option v-for="item in createByList" :key="item" :value="item">{{ item }}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="展示状态">
              <a-select v-model="queryParam.displayStatus" placeholder="请选择是否展示" allowClear>
                <a-select-option value="1">展示</a-select-option>
                <a-select-option value="0">不展示</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="16">
            <a-form-item label="创建日期">
              <j-date placeholder="请选择开始日期" class="query-group-cust" v-model="queryParam.createTime_begin"></j-date>
              <span class="query-group-split-cust"></span>
              <j-date placeholder="请选择结束日期" class="query-group-cust" v-model="queryParam.createTime_end"></j-date>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="月激活达标标准（%）">
              <a-select v-model="queryParam.monthStandard" placeholder="请选择月激活达标标准" allowClear>
                <a-select-option v-for="item in monthStandardList" :key="item" :value="item">{{
                    item + "%"
                  }}
                </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
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
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('分成奖励表')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl"
                @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel">
            <a-icon type="delete"/>
            删除
          </a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作
          <a-icon type="down"/>
        </a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a
        style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
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
          <img v-else :src="getImgView(text)" height="25px" alt="图片不存在"
               style="max-width:80px;font-size: 12px;font-style: italic;"/>
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

          <a-divider type="vertical"/>
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down"/></a>
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

    <electronShareRewards-modal ref="modalForm" @ok="modalFormOk"></electronShareRewards-modal>
  </a-card>
</template>

<script>
let rewardsList = [];

import {JeecgListMixin} from '@/mixins/JeecgListMixin'
import ElectronShareRewardsModal from './modules/ElectronShareRewardsModal'
import JDictSelectTag from '@/components/dict/JDictSelectTag.vue'
import JDate from '@/components/jeecg/JDate.vue'
import {initDictOptions, filterDictText} from '@/components/dict/JDictSelectUtil'
import JInput from '@/components/jeecg/JInput.vue'
import {getAction} from "@api/manage";

export default {
  name: "ElectronShareRewardsList",
  mixins: [JeecgListMixin],
  components: {
    JDictSelectTag,
    JDate,
    ElectronShareRewardsModal,
    JInput
  },
  data() {
    return {
      createByList: [],
      monthStandardList: [],
      belongAreaList: [],
      description: '分成奖励表管理页面',

      // 表头
      columns: [
        {
          title: '#',
          dataIndex: '',
          key: 'rowIndex',
          width: 60,
          align: "center",
          customRender: function (t, r, index) {
            return parseInt(index) + 1;
          }
        },
        {
          title: '分成奖励名称',
          align: "center",
          dataIndex: 'rewardsName',
        },
        {
          title: '运营商',
          align: "center",
          dataIndex: 'operatorName',
          customRender: function (text) {
            return text == "unicom" ? "联通" : (text == "mobile" ? "移动" : "电信")
          }
        },
        {
          title: '省市',
          align: "center",
          dataIndex: 'belongArea'
        },
        {
          title: '月发展量（激活用户数）',
          align: "center",
          dataIndex: 'monthDevelopment',
          customRender: function (text) {
            const textArr = text.split('\n')
            return (<div>
              {
                textArr.map(t => {
                  return (<li>{t}</li>)
                })
              }
            </div>)
          },
          customCell: () => {
            return {
              style: {
                'text-align': "left"
              }
            }
          }
        },
        {
          title: '分成比例',
          align: "center",
          dataIndex: 'shareProportion',
          customRender: function (text) {
            const textArr = text.split('\n')
            return (<div>
              {
                textArr.map(t => {
                  return (<li>{t}</li>)
                })
              }
            </div>)
          },
        },
        {
          title: '生效日期',
          align: "center",
          dataIndex: 'effectiveDate',
        },
        {
          title: '分成月数',
          align: "center",
          dataIndex: 'dividedMonths',
        },
        {
          title: '月激活达标标准',
          align: "center",
          dataIndex: 'monthStandard',
          customRender: function (text) {
            return text + "%"
          }
        },
        {
          title: '展示状态',
          align: "center",
          dataIndex: 'displayStatus',
          customRender: function (text) {
            return text == 1 ? "展示" : "不展示";
          }
        },
        {
          title: '创建人',
          align: "center",
          dataIndex: 'createBy',
        },
        {
          title: '创建日期',
          align: "center",
          dataIndex: 'createTime',
        },
        {
          title: '操作',
          dataIndex: 'action',
          align: "center",
          scopedSlots: {customRender: 'action'},
        }
      ],
      url: {
        list: "/sharerewards/electronShareRewards/list",
        delete: "/sharerewards/electronShareRewards/delete",
        deleteBatch: "/sharerewards/electronShareRewards/deleteBatch",
        exportXlsUrl: "/sharerewards/electronShareRewards/exportXls",
        importExcelUrl: "sharerewards/electronShareRewards/importExcel",
      },
      dictOptions: {
        createBy: [],
        operatorName: [],
        displayStatus: [],
        monthStandard: [],
      },

    }
  },
  computed: {
    importExcelUrl: function () {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
  methods: {
    initDictConfig() {
    },
    operatorChange(value) {
      let params = {operatorName: value};
      var _this = this;
      this.$set(this.queryParam, 'belongArea', undefined)
      getAction('/sharerewards/electronShareRewards/queryBelongAreaByOperator', params).then((res) => {
        if (res.success) {
          _this.belongAreaList = res.result;
          // console.log(_this.belongAreaList)
        } else {
          this.$message.warn(res.message)
        }
      })
    },
    queryCreateByList() {
      var _this = this;
      getAction('/sharerewards/electronShareRewards/queryCreateByList', null).then((res) => {
        if (res.success) {
          _this.createByList = res.result;
        } else {
          this.$message.warn(res.message)
        }
      })
    },
    querymonthStandardList() {
      var _this = this;
      getAction('/sharerewards/electronShareRewards/queryMonthStandardList', null).then((res) => {
        if (res.success) {
          _this.monthStandardList = res.result;
        } else {
          this.$message.warn(res.message)
        }
      })
    },
  },
  created() {
    this.queryCreateByList();
    this.querymonthStandardList();
    this.operatorChange();
  }
}
</script>
<style scoped>
@import '~@assets/less/common.less';
</style>