<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="10" :sm="10">
            <a-form-item label="提交时间">
              <j-date placeholder="请选择开始日期" class="query-group-cust" v-model="queryParam.createTime_begin" dateFormat="YYYY-MM-DD 00:00:00"></j-date>
              <span class="query-group-split-cust"></span>
              <j-date placeholder="请选择结束日期" class="query-group-cust" v-model="queryParam.createTime_end" dateFormat="YYYY-MM-DD 23:59:59"></j-date>
            </a-form-item>
          </a-col>

          <!--<a-col v-has="'realNmaeList:selectCustomerIdDls'" :md="6" :sm="8">
            <a-form-item label="客户">
              <j-tree-select
                v-model="queryParam.userId"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                pidField="create_by_id"
                :pidValue="fathid" />
            </a-form-item>
          </a-col>-->
          <a-col :md="5" :sm="6">
            <a-form-item label="ICCID">
              <a-input placeholder="请输入ICCID" v-model="queryParam.iccid"></a-input>
            </a-form-item>
          </a-col>
          <a-col v-has="'realNmaeList:selectCustomerId'" :md="5" :sm="6">
            <a-form-item label="客户" >
              <j-tree-select
                v-model="queryParam.userId"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                condition='{"del_flag":"0"}'
                pidField="create_by_id"
                pidValue=""/>
            </a-form-item>
          </a-col>

          <a-col v-has="'realNmaeList:selectCustomerIdDls'" :md="5" :sm="6">
            <a-form-item label="客户">
              <a-select v-model="queryParam.userId"
                        placeholder="请选择客户">
                <a-select-option v-for="d in userData" :key="d.value" :value="d.value">{{d.text}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="运营商类型">
              <j-dict-select-tag placeholder="请选择运营商类型" v-model="queryParam.operatorType" dict-code="operator_type"></j-dict-select-tag>
            </a-form-item>
          </a-col>

            <a-col :md="5" :sm="6">
              <a-form-item label="身份证号码">
                <a-input placeholder="请输入身份证号码" v-model="queryParam.idCardNumber"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="4" :sm="6">
              <a-form-item label="手机号">
                <a-input placeholder="请输入手机号" v-model="queryParam.mobile"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="4" :sm="6">
              <a-form-item label="状态">
                <a-select v-model="queryParam.status" placeholder="请选择状态">
                  <a-select-option value="0">待审核</a-select-option>
                  <a-select-option value="1">通过</a-select-option>
                  <a-select-option value="2">驳回</a-select-option>
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
      <!--<a-button @click="handleImportXls" type="primary" icon="plus">新增</a-button>-->
      <a-button type="primary" icon="download" @click="handleExportXls('实名查看')" :disabled="isDisable">导出</a-button>
      <!--<a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>-->
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

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
        <template slot="status" slot-scope="state">
          <a-tag v-if="state==0" color="gray">待审核</a-tag>
          <a-tag v-if="state==1" color="green">通过</a-tag>
          <a-tag v-if="state!=0 && state!=1 && state!=8" color="red">驳回</a-tag>
          <a-tag v-if="state==8" color="gray">待人工审核</a-tag>
        </template>
        <span slot="action" slot-scope="text, record">
          <!--<a @click="handleDetail(record)">详情</a>-->
          <a v-has="'realName:artificial'" v-if="record.operatorType==2 && record.status=='0'" @click="artificial(record)">转人工<a-divider type="vertical" /></a>
          <a-popconfirm title="确定驳回吗?" @confirm="() => rejected(record)">
            <a v-has="'realName:rejected'" v-if="(record.operatorType==2 ||record.operatorType==3)&& record.status=='0'" >驳回<a-divider type="vertical" /></a>
          </a-popconfirm>
          <a-popconfirm title="确定重新认证吗?" @confirm="() => rejected(record)">
            <a v-has="'realName:reload'" v-if="record.operatorType==1 && (record.status=='1'||record.status=='0')" >重新认证<a-divider type="vertical" /></a>
          </a-popconfirm>
          <a-popconfirm title="确定清除实名信息吗?" @confirm="() => removeRealName(record)">
            <a v-has="'realName:removeRealName'" v-if="record.operatorType==3 && record.status=='1'">清除实名<a-divider type="vertical" /></a>
          </a-popconfirm>
          <a v-has="'realName:updateStatus'" v-if="record.operatorType==3 && record.status=='0'" @click="updateStatus(record)">状态更新</a>
          <!--<a v-if="record.operatorType==1" @click="check(record)"><a-divider type="vertical" />审核</a>-->
          <!--<a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>-->
        </span>

      </a-table>
    </div>

    <realNameSystem-modal ref="modalForm" @ok="modalFormOk"></realNameSystem-modal>
    <real-name-system-edit-modal ref="editForm" @ok="modalFormOk"></real-name-system-edit-modal>
    <j-import-modal ref="importModal" :url="getImportUrl()" @ok="importOk"></j-import-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import { getAction,downFile } from '@/api/manage'
  import RealNameSystemModal from './modules/RealNameSystemModal'
  import RealNameSystemEditModal from './modules/RealNameSystemEditModal'
  import JDictSelectTag from '@/components/dict/JDictSelectTag.vue'
  import JDate from '@/components/jeecg/JDate.vue'
  import JImportModal from './modules/RealNameImportModal'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import {queryLowerAgent} from '@/api/api'

  export default {
    name: "RealNameSystemList",
    mixins:[JeecgListMixin],
    components: {
      JDictSelectTag,
      RealNameSystemModal,
      RealNameSystemEditModal,
      JDate,
      JImportModal,
      JTreeSelect,
      queryLowerAgent
    },
    data () {
      return {
        fathid: '',
        userData: [],
        description: '实名查看管理页面',
        formData: {},
        isDisable: false,
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
            title:'ICCID',
            align:"center",
            dataIndex: 'iccid'
          },
          {
            title:'MSISDN',
            align:"center",
            dataIndex: 'msisdn'
          },
          {
            title:'运营商类型',
            align:"center",
            dataIndex: 'operatorType_dictText',

          },
          {
            title:'身份证号码',
            align:"center",
            dataIndex: 'idCardNumber'
          },
          {
            title:'手机号',
            align:"center",
            dataIndex: 'mobile'
          },
          {
            title:'商户名',
            align:"center",
            dataIndex: 'userCompany'
          },
          {
            title:'状态',
            align:"center",
            dataIndex: 'status',
            scopedSlots: { customRender: 'status' }
            /*customRender:(text)=>{
              if(text=='0'){
                return "待审核";
              }else if(text=="1"){
                return "通过";
              } else if(text=="2"){
                return "驳回";
              } else {
                return text;
              }
            }*/
          },
          {
            title:'审核回执',
            align:"center",
            dataIndex: 'errorMsg'
          },
          {
            title:'备注',
            align:"center",
            dataIndex: 'remark',
            width:100
          },
          {
            title:'认证日期',
            align:"center",
            dataIndex: 'createTime',
            width:200
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            width:120,
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/realname/realNameSystem/list",
          query: "/realname/realNameSystem/queryUser",
          delete: "/realname/realNameSystem/delete",
          deleteBatch: "/realname/realNameSystem/deleteBatch",
          exportXlsUrl: "/realname/realNameSystem/exportXls",
          importExcelUrl: "realname/realNameSystem/importExcel",
          check: "/realname/realNameSystem/check",
          reject: "/realname/realNameSystem/reject",
          updateStatus: "realname/realNameSystem/updateStatus",
          removeRealName: "realname/realNameSystem/removeRealName"
        },
        dictOptions:{
         userCompany:[],
         status:[],
        },
        queryParam: {
          createTime_begin: this.dateFormat(new Date()),
          createTime_end: this.dateFormat(new Date())
        }
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    watch(){

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
      /*getAction("/realname/realNameSystem/queryUser").then((res)=>{
        if(res.success){
          this.fathid=res.result.id
          console.log('this.uid>>>>>'+this.fathid)
        }
      });*/
    },
    methods: {
      initDictConfig(){
        initDictOptions('').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'userCompany', res.result)
          }
        })
        initDictOptions('').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'status', res.result)
          }
        })
      },
      check: function (record) {
        this.$refs.editForm.edit(record);
        this.$refs.editForm.title = "实名制审核";
        this.$refs.editForm.disableSubmit = false;
      },
      artificial: function (record) {
        let params = {id:record.id};
        getAction(this.url.check,params).then((res)=>{
          if(res.success){
            this.$message.success(res.message);
            this.loadData();
          }else{
            this.$message.warning(res.message);
            this.loadData();
          }
        })
      },
      rejected: function (record) {
        let params = {id:record.id};
        getAction(this.url.reject,params).then((res)=>{
          if(res.success){
            this.$message.success(res.message);
            this.loadData();
          }else{
            this.$message.warning(res.message);
          }
        })
      },
      updateStatus: function (record) {
        let params = {id:record.id};
        getAction(this.url.updateStatus,params).then((res)=>{
          if(res.success){
            this.$message.success(res.message);
            this.loadData();
          }else{
            this.$message.warning(res.message);
          }
        })
      },
      removeRealName: function (record) {
        let params = {id:record.id};
        getAction(this.url.removeRealName,params).then((res)=>{
          if(res.success){
            this.$message.success(res.message);
            this.loadData();
          }else{
            this.$message.warning(res.message);
          }
        })
      },
      importOk(){
        this.loadData(1)
      },
      getImportUrl(){
        return '/realname/realNameSystem/importExcel/'
      },
      handleExportXls(fileName){
        this.isDisable = true;
        if(!fileName || typeof fileName != "string"){
          fileName = "导出文件"
        }
        let param = {...this.queryParam};
        if(this.selectedRowKeys && this.selectedRowKeys.length>0){
          param['selections'] = this.selectedRowKeys.join(",")
        }
        console.log("导出参数",param)
        downFile(this.url.exportXlsUrl,param).then((data)=>{
          setTimeout(() => {
            this.isDisable = false;
            if (!data) {
              this.$message.warning("文件下载失败")
              return
            }
            if (typeof window.navigator.msSaveBlob !== 'undefined') {
              window.navigator.msSaveBlob(new Blob([data]), fileName+'.xls')
            }else{
              let url = window.URL.createObjectURL(new Blob([data]))
              let link = document.createElement('a')
              link.style.display = 'none'
              link.href = url
              link.setAttribute('download', fileName+'.xls')
              document.body.appendChild(link)
              link.click()
              document.body.removeChild(link); //下载完成移除元素
              window.URL.revokeObjectURL(url); //释放掉blob对象
            }
          },1000)

        })
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>