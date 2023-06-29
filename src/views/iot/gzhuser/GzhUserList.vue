<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
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
          <a-col :md="6" :sm="8">
            <a-form-item label="用户昵称">
              <a-input placeholder="请输入用户昵称" v-model="queryParam.nickName" allowClear="true"></a-input>
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
    

    <!-- table区域-begin -->
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

    <gzhUser-modal ref="modalForm" @ok="modalFormOk"></gzhUser-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import GzhUserModal from './modules/GzhUserModal'
  import { getAction } from '@/api/manage'

  export default {
    name: "GzhUserList",
    mixins:[JeecgListMixin],
    components: {
      GzhUserModal
    },
    data () {
      return {
        description: 'gzh_user管理页面',

        // 表头
        columns: [
          {
            title:'openId',
            align:"center",
            dataIndex: 'openId'
          },
          {
            title:'昵称',
            align:"center",
            dataIndex: 'nickName'
          },
          {
            title:'性别',
            align:"center",
            dataIndex: 'sex',
            customRender:function (text) {
              if(text==1){
                return "男";
              }else if(text==2){
                return "女";
              } else if(text==0){
                return "未知";
              } else {
                return text;
              }
            }
          },
          {
            title:'省',
            align:"center",
            dataIndex: 'province'
          },
          {
            title:'市',
            align:"center",
            dataIndex: 'city'
          },
          {
            title:'国家',
            align:"center",
            dataIndex: 'country'
          },
          {
            title:'头像url',
            align:"center",
            dataIndex: 'headImgurl',
            customRender: function (t,r, index) {
              if (t !== null && t !== "" && t !== undefined) {
                return <img src={t} style="width: 200px; height: 100px; "/>
              }
            }
          },
          {
            title:'公众号',
            align:"center",
            dataIndex: 'appId_dictText'
          },
          {
            title:'是否下单',
            align:"center",
            dataIndex: 'status',
            customRender:function (text) {
              if(text==1){
                return "已下单";
              }else if(text==0){
                return "未下单";
              } else {
                return text;
              }
            }
          },
          // {
          //   title: '操作',
          //   dataIndex: 'action',
          //   align:"center",
          //   scopedSlots: { customRender: 'action' }
          // }
        ],
        url: {
          list: "/gzhuser/gzhUser/list",
          delete: "/gzhuser/gzhUser/delete",
          deleteBatch: "/gzhuser/gzhUser/deleteBatch",
          exportXlsUrl: "/gzhuser/gzhUser/exportXls",
          importExcelUrl: "gzhuser/gzhUser/importExcel",
          initMchUrl: "/wechatpay/iotWechatPay/initMchNameCompany",
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
        getAction(this.url.initMchUrl).then((res)=>{
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