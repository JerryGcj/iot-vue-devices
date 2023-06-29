<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="触点编码">
              <j-input placeholder="请输入触点编码" v-model="queryParam.touchApplyId"></j-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="产品id">
              <j-input placeholder="产品id" v-model="queryParam.product"></j-input>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :md="6" :sm="8">
              <a-form-item label="订购号码">
                <j-input placeholder="请输入订购号码" v-model="queryParam.mobile"></j-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="联系人号码">
                <j-input placeholder="请输入联系人号码" v-model="queryParam.contactNumber"></j-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="订单状态">
                <a-select v-model="queryParam.state" placeholder="请选择订单状态" allowClear>
                  <a-select-option value="0">下单</a-select-option>
                  <a-select-option value="1">激活</a-select-option>
                  <a-select-option value="6">首充</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="消息创建时间">
                <j-date :show-time="true" v-model="queryParam.time_begin" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
                <span style="width: 10px;"> ~ </span>
                <j-date :show-time="true" v-model="queryParam.time_end" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择结束时间"></j-date>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="下单时间">
                  <j-date :show-time="true" v-model="queryParam.createTime_begin" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择开始时间" ></j-date>
                  <span style="width: 10px;"> ~ </span>
                  <j-date :show-time="true" v-model="queryParam.createTime_end" date-format="YYYY-MM-DD HH:mm:ss" class="query-group-cust" placeholder="请选择结束时间"></j-date>
                </a-form-item>
            </a-col>
          </template>
          <a-col :md="6" :sm="8">
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
      <!--      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>-->
      <a-button type="primary" icon="download" @click="handleExportXls('码良拉取订单数据表')">导出</a-button>
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
        :rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"

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
<!--          <a @click="handleEdit(record)">编辑</a>-->

          <!--          <a-divider type="vertical" />-->
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

    <ldltMlOrder-modal ref="modalForm" @ok="modalFormOk"></ldltMlOrder-modal>
  </a-card>
</template>

<script>

import {JeecgListMixin} from '@/mixins/JeecgListMixin'
import LdltMlOrderModal from './modules/LdltMlOrderModal'
import JDate from '@/components/jeecg/JDate.vue'
import JInput from '@/components/jeecg/JInput.vue'
import JMultiSelectTag from '@/components/dict/JMultiSelectTag'

export default {
  name: "LdltMlOrderList",
  mixins: [JeecgListMixin],
  components: {
    JInput,
    JDate,
    LdltMlOrderModal,
    JMultiSelectTag,
  },
  data() {
    return {
      description: '码良拉取订单数据表管理页面',
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
          title: '触点编码',
          align: "center",
          dataIndex: 'touchApplyId'
        },
        {
          title: '产品id',
          align: "center",
          dataIndex: 'product'
        },
        {
          title: '订购号码',
          align: "center",
          dataIndex: 'mobile'
        },
        {
          title: '联系人号码',
          align: "center",
          dataIndex: 'contactNumber'
        },
        {
          title: '消息id',
          align: "center",
          dataIndex: 'messageId'
        },
        {
          title: '订单状态',
          align: "center",
          dataIndex: 'state',
          customRender: function (text) {
            switch (text) {
              case "0":
                return "下单"
              case "1":
                return "激活"
              case "6":
                return "首充"
            }
          }
        },
        {
          title: '消息创建时间',
          align: "center",
          dataIndex: 'time',
          // customRender: function (text) {
          //   return !text ? "" : (text.length > 10 ? text.substr(0, 10) : text)
          // }
        },
        {
          title: '商城订单号',
          align: "center",
          dataIndex: 'orderId'
        },
        {
          title: '下单时间',
          align: "center",
          dataIndex: 'createTime',
        },
        {
          title: '操作',
          dataIndex: 'action',
          align: "center",
          scopedSlots: {customRender: 'action'}
        }
      ],
      url: {
        list: "/ldltmlorder/ldltMlOrder/list",
        delete: "/ldltmlorder/ldltMlOrder/delete",
        deleteBatch: "/ldltmlorder/ldltMlOrder/deleteBatch",
        exportXlsUrl: "/ldltmlorder/ldltMlOrder/exportXls",
        importExcelUrl: "ldltmlorder/ldltMlOrder/importExcel",
      },
      dictOptions: {},
    }
  },
  computed: {
    importExcelUrl: function () {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
  methods: {
    initDictConfig() {
    }
  }
}
</script>
<style scoped>
@import '~@assets/less/common.less';
</style>