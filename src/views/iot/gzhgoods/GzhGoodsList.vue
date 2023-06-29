<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="产品名称">
              <a-select
                allowClear="true"
                show-search
                v-model="queryParam.agentId"
                style="width: 100%"
                placeholder="请选择"
                :options="this.dictOperatorOptions"
                :filterOption="likeQuery"
                :autoClearSearchValue="false"
                :maxTagTextLength=3
                :maxTagCount=3
              ></a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="公众号">
              <a-select
                allowClear="true"
                show-search
                v-model="queryParam.userId"
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

          <a-col :md="6" :sm="8">
            <a-form-item label="状态">
              <a-select  v-model="queryParam.status" allowClear="true" placeholder="请输入状态">
                <a-select-option value="0">上架</a-select-option>
                <a-select-option value="1">下架</a-select-option>
              </a-select>
            </a-form-item>

          </a-col>

          <a-col :md="4" :sm="6" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search" style="margin-left: 8px">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button @click="handleAdd" type="primary" icon="plus" style="margin-left: 8px">新增</a-button>

            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
<!--      <a-button type="primary" icon="download" @click="handleExportXls('gzh_goods')">导出</a-button>-->
<!--      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
<!--        <a-button type="primary" icon="import">导入</a-button>-->
<!--      </a-upload>-->
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
          <a @click="handleEdit(record)">编辑<a-divider type="vertical" /></a>
          <a @click="handleImport(record)">修改图片</a>

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

    <gzhGoods-modal ref="modalForm" @ok="modalFormOk"></gzhGoods-modal>
    <gzh-goods-import-picture-modal ref="importPictureModal" @ok="modalFormOk"></gzh-goods-import-picture-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import GzhGoodsModal from './modules/GzhGoodsModal'
  import GzhGoodsImportPictureModal from "./modules/GzhGoodsImportPictureModal";
  import { getAction } from '@/api/manage'

  export default {
    name: "GzhGoodsList",
    mixins:[JeecgListMixin],
    components: {
      GzhGoodsModal,
      GzhGoodsImportPictureModal
    },
    data () {
      return {
        description: 'gzh_goods管理页面',
        // 表头
        columns: [
          {
            userId:"",
            dictOptions:[],
            dictOperatorOptions:[],
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
            title:'产品名称',
            align:"center",
            dataIndex: 'agentId_dictText'
          },
          {
            title:'公众号',
            align:"center",
            dataIndex: 'userId_dictText'
          },
          {
            title:'产品标题',
            align:"center",
            dataIndex: 'title'
          },
          {
            title:'状态',
            align:"center",
            dataIndex: 'status',
            customRender:function (text) {
              if(text=='0'){
                return "上架";
              }else if(text=="1"){
                return "下架";
              } else {
                return text;
              }
            }
          },
          {
            title:'排序',
            align:"center",
            dataIndex: 'sort'
          },
          {
            title:'图片',
            align:"center",
            dataIndex: 'imageUrl',
            customRender: function (t,r, index) {
              if (t !== null && t !== "" && t !== undefined) {
                return <img src={t} style="width: 200px; height: 100px; "/>
              }
            }
          },
          {
            title:'点击跳转页面地址',
            align:"center",
            dataIndex: 'redirectUrl'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/gzhgoods/gzhGoods/list",
          delete: "/gzhgoods/gzhGoods/delete",
          deleteBatch: "/gzhgoods/gzhGoods/deleteBatch",
          exportXlsUrl: "/gzhgoods/gzhGoods/exportXls",
          importExcelUrl: "gzhgoods/gzhGoods/importExcel",
          initOperatorUrl: "/electronchannelagent/electronChannelAgent/initOperator",
          initAgentUrl: "/sys/user/initUserCompany",

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
      this.initOperator();
      this.initAgent();
    },
    methods: {
      initDictConfig(){
      },
      handleImport(record) {
        this.$refs.importPictureModal.show();
        this.$refs.importPictureModal.id = record.id;

      },
      initOperator() {
        getAction(this.url.initOperatorUrl).then((res) => {
          if (res.success) {
            this.dictOperatorOptions = res.result
          }
        })
      },
      likeQuery(input, option){
        return (option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0)
      },
      initAgent(selectValue){
        let params={selectValue:selectValue};
        getAction(this.url.initAgentUrl,params).then((res)=>{
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