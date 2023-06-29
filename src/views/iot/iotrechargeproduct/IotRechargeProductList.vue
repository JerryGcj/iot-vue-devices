<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="产品名称">
              <a-input placeholder="请输入产品名称" v-model="queryParam.name"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="状态">
              <a-select v-model="queryParam.status" placeholder="请选择">
                <a-select-option value="0">上架</a-select-option>
                <a-select-option value="1">下架</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
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
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('iot_recharge_product')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
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
        :rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        
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
        <template slot="status" slot-scope="state">
          <a-tag v-if="state==0" color="green">上架</a-tag>
          <a-tag v-if="state==1" color="red">下架</a-tag>
        </template>
        <template slot="ReturnCoupon" slot-scope="state">
          <a-tag v-if="state==0" color="green">赠送</a-tag>
          <a-tag v-if="state==1" color="red">不赠送</a-tag>
        </template>
      </a-table>
    </div>

    <iotRechargeProduct-modal ref="modalForm" @ok="modalFormOk"></iotRechargeProduct-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import IotRechargeProductModal from './modules/IotRechargeProductModal'

  export default {
    name: "IotRechargeProductList",
    mixins:[JeecgListMixin],
    components: {
      IotRechargeProductModal
    },
    data () {
      return {
        description: '充值产品管理页面',
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
            dataIndex: 'operatorType_dictText'
          },{
            title:'产品名称',
            align:"center",
            dataIndex: 'name'
          },
          {
            title:'面额(元)',
            align:"center",
            dataIndex: 'money'
          },
          /*{
            title:'推广员返佣(元)',
            align:"center",
            dataIndex: 'promoterRebate'
          },
          {
            title:'店铺返佣(元)',
            align:"center",
            dataIndex: 'storeRebate'
          },*/
          {
            title: '是否赠送',
            align:"center",
            dataIndex: 'handsel',
            scopedSlots: { customRender: 'ReturnCoupon' }
          },
          {
            title:'赠送方式',
            align:"center",
            dataIndex: 'givingType',
            customRender:function (text) {
              if(text=="0"){
                return "优惠券";
              }
              if(text=="1"){
                return "现金余额";
              }
              if(text=="2"){
                return "套餐体验包";
              }
            }
          },
          {
            title:'用户返优惠券(元)',
            align:"center",
            dataIndex: 'couponMoney'
          },
          {
            title:'优惠券张数',
            align:"center",
            dataIndex: 'couponCount'
          },
          {
            title:'优惠券最小使用金额(元)',
            align:"center",
            dataIndex: 'couponLimit'
          },
          {
            title:'赠送金额(元)',
            align:"center",
            dataIndex: 'givingMoney'
          },
          {
            title:'赠送套餐',
            align:"center",
            dataIndex: 'packageId_dictText'
          },
          {
            title:'企业名称',
            align:"center",
            dataIndex: 'userId_dictText'
          },
          {
            title: '状态',
            align:"center",
            dataIndex: 'status',
            scopedSlots: { customRender: 'status' }
          },

          // {
          //   title:'备注',
          //   align:"center",
          //   dataIndex: 'note'
          // },
          {
            title:'添加时间',
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
          list: "/iotrechargeproduct/iotRechargeProduct/list",
          delete: "/iotrechargeproduct/iotRechargeProduct/delete",
          deleteBatch: "/iotrechargeproduct/iotRechargeProduct/deleteBatch",
          exportXlsUrl: "/iotrechargeproduct/iotRechargeProduct/exportXls",
          importExcelUrl: "iotrechargeproduct/iotRechargeProduct/importExcel",
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
      initDictConfig(){
      }
       
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>