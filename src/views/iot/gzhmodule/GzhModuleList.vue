<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="公众号">
              <j-search-select-tag
                placeholder="请选择公众号"
                v-model="queryParam.appId"
                dict="iot_wechat_pay,mch_name,app_id">
              </j-search-select-tag>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="状态">
              <a-select  v-model="queryParam.status" allowClear placeholder="请输入状态" >
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
          <a @click="handleEdit(record)">编辑 <a-divider type="vertical" /></a>
          <a @click="handleOpen(record)">产品管理</a>

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

      <!-- 公众号配置产品抽屉弹框 -->
      <a-drawer
        :width="1000"
        placement="right"
        :closable="true"
        :maskClosable="false"
        :visible="visible"
        @close="handleClose"
      >
        <div class="table-page-search-wrapper">
          <a-form layout="inline">
            <a-row :gutter="24">

              <a-col :md="8" :sm="10">
                <a-form-item label="产品">
                  <j-search-select-tag
                    placeholder="请选择产品"
                    v-model="agentId"
                    dict="electron_channel_agent,agent_name,id">
                  </j-search-select-tag>
                </a-form-item>
              </a-col>

              <a-col :md="9" :sm="24">
                <a-button type="primary"  @click="searchQuery2" icon="search" style="margin-left: 21px">查询</a-button>
                <a-button type="primary" @click="searchReset2" icon="reload" style="margin-left: 8px">重置</a-button>
                <a-button type="primary" @click="addGoods"  style="margin-left: 8px">新增产品</a-button>
              </a-col>
            </a-row>
          </a-form>

          <a-table
            style="height:500px"
            ref="table2"
            bordered
            size="middle"
            rowKey="id"
            :columns="columns2"
            :dataSource="dataSource2"
            :pagination="ipagination2"
            @change="handleTableChange2"

          >
        <span slot="action" slot-scope="text, record">
          <a-popconfirm title="确定删除吗?" @confirm="() => handleDeleteGzhModuleGoods(record.id)">
            <a>删除 <a-divider type="vertical" /></a>
          </a-popconfirm>
                    <a @click="addGoods(record.id)">编辑</a>

        </span>

          </a-table>
        </div>

      </a-drawer>



    </div>

    <gzhModule-modal ref="modalForm" @ok="modalFormOk"></gzhModule-modal>
    <add-goods-modal ref="addGoods" @ok="addGoodsModalOk"></add-goods-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import GzhModuleModal from './modules/GzhModuleModal'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import { getAction,deleteAction } from '@/api/manage'
  import addGoodsModal from "./modules/addGoodsModal";


  export default {
    name: "GzhModuleList",
    mixins:[JeecgListMixin],
    components: {
      GzhModuleModal,
      JSearchSelectTag,
      addGoodsModal
    },
    data () {
      return {
        description: 'gzh_module管理页面',
        selectedRowKeys2: [],
        selectionRows2: [],
        moduleId: "",
        appId:"",
        agentId:'',
        visible: false,
        loading2: false,
        dataSource2:[],
        ipagination2: {
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + '-' + range[1] + ' 共' + total + '条'
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        // 表头
        columns: [
          {
            title:'公众号ID',
            align:"center",
            dataIndex: 'appId_dictText'
          },
          {
            title:'模块标题',
            align:"center",
            dataIndex: 'title'
          },
          {
            title:'排序',
            align:"center",
            dataIndex: 'sort'
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
            title: '创建时间',
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
        columns2: [
          {
            title: '产品名称',
            align: 'center',
            dataIndex: 'agentId_dictText',
          },
          {
            title: '产品标题',
            align: 'center',
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
            title: '排序',
            align: 'center',
            dataIndex: 'sort'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            width:90,
            scopedSlots: { customRender: 'action' },
          }
        ],
        url: {
          list: "/gzhmodule/gzhModule/list",
          list2: "/gzhmodulegoods/gzhModuleGoods/listByModuleId",
          delete: "/gzhmodule/gzhModule/delete",
          deleteBatch: "/gzhmodule/gzhModule/deleteBatch",
          exportXlsUrl: "/gzhmodule/gzhModule/exportXls",
          importExcelUrl: "gzhmodule/gzhModule/importExcel",
          deleteGzhModuleGoods: "/gzhmodulegoods/gzhModuleGoods/delete",
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
      handleDeleteGzhModuleGoods(id) {
        debugger
        if(!this.url.deleteGzhModuleGoods){
          this.$message.error("请设置url.delete属性!")
          return
        }
        var that = this;
        deleteAction(that.url.deleteGzhModuleGoods, {id: id}).then((res) => {
          if (res.success) {
            that.$message.success(res.message);
            that.searchQuery2();
          } else {
            that.$message.warning(res.message);
          }
        });

      },
      handleTableChange2(pagination, filters, sorter) {
        //分页、排序、筛选变化时触发
        //TODO 筛选
        if (Object.keys(sorter).length > 0) {
          this.isorter2.column = sorter.field
          this.isorter2.order = 'ascend' == sorter.order ? 'asc' : 'desc'
        }
        debugger
        this.ipagination2 = pagination
        this.loadData2(2,this.moduleId)
      },
      onSelectChange2(selectedRowKeys, selectionRows) {
        this.selectedRowKeys2 = selectedRowKeys
        this.selectionRows2 = selectionRows
      },
      onClearSelected2() {
        this.selectedRowKeys2 = []
        this.selectionRows2 = []
      },
      addGoods(id) {
        if (typeof id === 'string') {
          this.$refs.addGoods.edit(false,id);
        } else {
          this.$refs.addGoods.edit(this.appId,id);
        }
        this.$refs.addGoods.title = "产品配置";
        this.$refs.addGoods.moduleId = this.moduleId
        this.$refs.addGoods.appId = this.appId
        // this.$refs.addGoods.disableSubmit = false;
      },
      searchQuery2() {
        this.loadData2(1,this.moduleId)
      },
      addGoodsModalOk() {
        this.searchQuery2();
      },
      //重置
      searchReset2() {
        this.agentId = ""
        this.loadData2(1,this.moduleId)
      },
      handleClose() {
        this.visible  = false;
        this.agentId = "";
        this.appId = ""
        this.moduleId = ""
      },
      handleOpen(record) {
        this.visible = true
        this.moduleId = record.id
        this.appId = record.appId
        this.loadData2(1,this.moduleId)
      },
      initDictConfig(){
      },
      loadData2(page,moduleId) {
        const params = new FormData
        params.append("moduleId",moduleId);
        if (this.agentId === undefined) {
          this.agentId = ''
        }
        // if (page === 1) {
        //   if (this.ipagination2.current) {
        //     this.ipagination2.current = 1
        //   }
        // }
        getAction('gzhmodulegoods/gzhModuleGoods/listByModuleId?moduleId='+moduleId+'&agentId='+this.agentId+'&pageNo='+this.ipagination2.current+'&pageSize='+this.ipagination2.pageSize,null).then((res) => {
          if (res.success) {
            this.dataSource2 = res.result.records
            this.ipagination2.total = res.result.total
          }
          this.loading2 = false
        })
      },

       
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>