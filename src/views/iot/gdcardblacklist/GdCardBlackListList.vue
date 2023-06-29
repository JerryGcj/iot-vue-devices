<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6">
            <a-form-item label="客户姓名">
              <a-input placeholder="请输入客户姓名" v-model="queryParam.cusName" allowClear></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="6">
            <a-form-item label="客户手机号">
              <a-input placeholder="请输入客户手机号" v-model="queryParam.cusPhone" allowClear></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="6">
            <a-form-item label="客户身份证号">
              <a-input placeholder="请输入客户身份证号" v-model="queryParam.cusIdno" allowClear></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="6">
            <a-form-item label="进入黑名单原因">
              <a-input placeholder="请输入进入黑名单原因" v-model="queryParam.filterMsg" allowClear></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button @click="handleAdd" type="primary" icon="plus" style="margin-left: 8px">新增</a-button>
              <a-button type="primary" icon="import" @click="handleImportXls" style="margin-left: 8px" >导入</a-button>
              <a-button type="primary" icon="download" @click="handleExportXls('黑名单列表')" :loading="downloading" style="margin-left: 8px">{{ downloading ? '导出中...' : '导出' }}</a-button>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
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

        <template slot="filterFlag" slot-scope="text, record, index">
          <a-switch checkedChildren="是" unCheckedChildren="否" v-model="record.filterFlag" @change="filterFlagChange" @click="filterFlagSwitch(record.id)" />
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

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑<a-divider type="vertical" /></a>
          <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
            <a>删除</a>
          </a-popconfirm>
        </span>

      </a-table>
    </div>

    <gdCardBlackList-modal ref="modalForm" @ok="modalFormOk"></gdCardBlackList-modal>
    <gd-card-black-import-modal ref="GdCardBlackImportModal" @ok="modalFormOk"></gd-card-black-import-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import GdCardBlackListModal from './modules/GdCardBlackListModal'
  import GdCardBlackImportModal from './modules/GdCardBlackImportModal'
  import {putAction, getAction, downFile} from '@/api/manage';

  export default {
    name: "GdCardBlackListList",
    mixins:[JeecgListMixin],
    components: {
      GdCardBlackListModal,
      GdCardBlackImportModal
    },
    data () {
      return {
        description: 'gd_card_black_list管理页面',
        downloading: false,
        // 表头
        columns: [
          {
            filterFlagChecked: "",
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
            title:'商品',
            align:"center",
            dataIndex: 'agentId_dictText'
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
            title:'用户的客户端IP',
            align:"center",
            dataIndex: 'payerClientIp'
          },
          {
            title:'详细地址',
            align:"center",
            dataIndex: 'detailAddr'
          },
          {
            title: '是否开启过滤',
            align: "center",
            width: 60,
            scopedSlots: {customRender: 'filterFlag'},
          },
          {
            title:'进入黑名单原因',
            align:"center",
            dataIndex: 'filterMsg'
          },
          {
            title:'进入时间',
            align:"center",
            dataIndex: 'createTime',
            width:200
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/gdcardblacklist/gdCardBlackList/list",
          delete: "/gdcardblacklist/gdCardBlackList/delete",
          deleteBatch: "/gdcardblacklist/gdCardBlackList/deleteBatch",
          exportXlsUrl: "/gdcardblacklist/gdCardBlackList/exportExcel",
          importExcelUrl: "gdcardblacklist/gdCardBlackList/importExcel",
          edit: "/gdcardblacklist/gdCardBlackList/edit",
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
      searchQuery() {
        this.loadData(1);
      },
      loadData(arg) {
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件
        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            this.dataSource = res.result.records;
            for (let i = 0; i < this.dataSource.length; i++) {
              if (this.dataSource[i].filterFlag === "1") {
                this.dataSource[i].filterFlag = true
              } else {
                this.dataSource[i].filterFlag = false
              }
            }
            this.ipagination.total = res.result.total;
          }
        })
      },

      initDictConfig(){
      },
      filterFlagChange(checked) {
        this.filterFlagChecked = checked;
      },
      filterFlagSwitch(id){
        const that = this;
        if (this.filterFlagChecked === true) {
          this.filterFlagChecked = "1"
        } else {
          this.filterFlagChecked = "0"
        }
        console.log(this.filterFlagChecked);
        putAction(this.url.edit,{filterFlag: this.filterFlagChecked,id: id}).then((res)=>{
          if(res.success){
            that.$message.success(res.message);
            that.$emit('ok');
            // this.initData();
          }else{
            that.$message.warning(res.message);
          }
        });
      },
      handleImportXls: function () {
        this.$refs.GdCardBlackImportModal.add();
        this.$refs.GdCardBlackImportModal.title = "导入";
        this.$refs.GdCardBlackImportModal.disableSubmit = false;
      },
      handleExportXls(fileName){
        this.downloading = true;
        if(!fileName || typeof fileName != "string"){
          fileName = "导出文件"
        }
        let param = {...this.queryParam};
        if(this.selectedRowKeys && this.selectedRowKeys.length>0){
          param['selections'] = this.selectedRowKeys.join(",")
        }
        console.log("导出参数",param)
        downFile(this.url.exportXlsUrl,param).then((data)=>{
          if (!data) {
            this.$message.warning("文件下载失败")
            this.downloading = false;
            return
          }
          if (typeof window.navigator.msSaveBlob !== 'undefined') {
            window.navigator.msSaveBlob(new Blob([data]), fileName+'.xls')
            this.downloading = false;
          }else{
            let url = window.URL.createObjectURL(new Blob([data]))
            let link = document.createElement('a')
            link.style.display = 'none'
            link.href = url
            link.setAttribute('download', fileName+'.xls')
            document.body.appendChild(link)
            link.click()
            this.downloading = false;
            document.body.removeChild(link); //下载完成移除元素
            window.URL.revokeObjectURL(url); //释放掉blob对象
          }
        })
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>