<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="客户姓名">
              <a-input  v-model="queryParam.cusName" allowClear="true" placeholder="请输入姓名">
              </a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="公众号">
              <a-select
                allowClear="true"
                show-search
                v-model="queryParam.appId"
                style="width: 100%"
                placeholder="请选择"
                :options="this.dictOptions"
                :filterOption="likeQuery"
                :autoClearSearchValue="false"
                :maxTagTextLength=3
                :maxTagCount=3
              ></a-select>
            </a-form-item>
          </a-col>

          <a-col :md="4" :sm="6" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search" style="margin-left: 8px">查询</a-button>
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
<!--      <a-button type="primary" icon="download" @click="handleExportXls('gzh_goods_order')">导出</a-button>-->
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

    <gzhGoodsOrder-modal ref="modalForm" @ok="modalFormOk"></gzhGoodsOrder-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import GzhGoodsOrderModal from './modules/GzhGoodsOrderModal'
  import { getAction } from '@/api/manage'

  export default {
    name: "GzhGoodsOrderList",
    mixins:[JeecgListMixin],
    components: {
      GzhGoodsOrderModal
    },
    data () {
      return {
        description: 'gzh_goods_order管理页面',
        // 表头
        columns: [
          // {
          //   title: '#',
          //   dataIndex: '',
          //   key:'rowIndex',
          //   width:60,
          //   align:"center",
          //   customRender:function (t,r,index) {
          //     return parseInt(index)+1;
          //   }
          // },
          {
            title:'产品名称',
            align:"center",
            dataIndex: 'agentName'
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
            title:'客户身份证号',
            align:"center",
            dataIndex: 'cusIdno'
          },
          {
            title:'公众号名称',
            align:"center",
            dataIndex: 'appId_dictText'
          },
          {
            title:'openId',
            align:"center",
            dataIndex: 'openId'
          },
          {
            title:'下单时间',
            align:"center",
            dataIndex: 'createTime'
          },
          // {
          //   title: '操作',
          //   dataIndex: 'action',
          //   align:"center",
          //   scopedSlots: { customRender: 'action' }
          // }
        ],
        url: {
          list: "/gzhgoodsorder/gzhGoodsOrder/list",
          delete: "/gzhgoodsorder/gzhGoodsOrder/delete",
          deleteBatch: "/gzhgoodsorder/gzhGoodsOrder/deleteBatch",
          exportXlsUrl: "/gzhgoodsorder/gzhGoodsOrder/exportXls",
          importExcelUrl: "gzhgoodsorder/gzhGoodsOrder/importExcel",
          initAgentUrl: "/wechatpay/iotWechatPay/initMchNameCompany",
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
    created () {
     this.initMch();
    },
    methods: {
      initDictConfig(){
      },
      likeQuery(input, option){
        return (option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0)
      },
      initMch(){
        getAction(this.url.initAgentUrl).then((res)=>{
          if(res.success){
            this.dictOptions = res.result;
          }
        })

      },
       
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>