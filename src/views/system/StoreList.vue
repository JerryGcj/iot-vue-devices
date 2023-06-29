<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <!--<a-col v-has="'userList:importExcelUrl'" :md="6" :sm="8">
            <a-form-item  label="企业" >
              <j-tree-select
                v-model="queryParam.id"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                pidField="create_by_id"
                pidValue=""
                multiple
                />
            </a-form-item>
          </a-col>-->

          <a-col :md="6" :sm="8">
            <a-form-item label="店铺名称">
              <a-input placeholder="店铺名称" v-model="queryParam.userCompany"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="联系人">
              <a-input placeholder="联系人" v-model="queryParam.realname"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button v-has="'promote:add'" type="primary" @click="handleAdd" icon="plus" style="margin-left: 8px">添加店铺</a-button>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>

    <a-modal
      :visible="modal.visible"
      :width="modal.width"
      :style="modal.style"
      @ok="()=>modal.visible=false"
      @cancel="()=>modal.visible=false">

      <template slot="title">
        <div style="width: 100%;height:20px;padding-right:32px;">
          <div style="float: left;">{{ modal.title }}</div>
          <div style="float: right;">
            <a-button
              icon="fullscreen"
              style="width:56px;height:100%;border:0"
              @click="handleClickToggleFullScreen"/>
          </div>
          <div class="no-print" style="text-align: right">
            <a-button v-print="'#printContent'" ghost type="primary">打印</a-button>
          </div>
        </div>
      </template>

      <section ref="print" id="printContent" class="abcdefg">
        <template v-for="(i,k) of qrcodeData"  >
          <img :src="i.storeQrcode"  >
        </template>
      </section>


    </a-modal>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i>已选择&nbsp;<a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项&nbsp;&nbsp;
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        bordered
        size="middle"
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"

        @change="handleTableChange">

        <template slot="avatarslot" slot-scope="text, record, index">
          <div class="anty-img-wrap">
            <a-avatar shape="square" :src="getAvatarView(record.avatar)" icon="user"/>
          </div>
        </template>

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑</a>
          <!--<a v-if="record.userType==2" @click="viewQrcode(record)">查看二维码</a>-->
          <a-divider type="vertical"/>

          <a-dropdown>
            <a class="ant-dropdown-link">
              更多 <a-icon type="down"/>
            </a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a href="javascript:;" @click="handleChangePassword(record.username)">重置密码</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a v-has="'user:delete'">删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>


      </a-table>
    </div>
    <!-- table区域-end -->

    <!--<user-modal ref="modalForm" @ok="modalFormOk"></user-modal>-->
    <password-modal ref="passwordmodal" @ok="passwordModalOk"></password-modal>
    <sys-user-agent-modal ref="sysUserAgentModal"></sys-user-agent-modal>
    <user-modal-dls ref="modalForm" @ok="modalFormOk"></user-modal-dls>
    <customerSalesDiscount-modal ref="customerSalesModalForm" @ok="modalFormOk"></customerSalesDiscount-modal>
    <terminalSalesDiscount-modal ref="terminalSalesModalForm" @ok="modalFormOk"></terminalSalesDiscount-modal>
  </a-card>
</template>

<script>
  import UserModal from './modules/UserModal'
  import PasswordModal from './modules/PasswordModal'
  import {putAction,getAction} from '@/api/manage';
  import {frozenBatch,queryLowerAgent} from '@/api/api'
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  import SysUserAgentModal from "./modules/SysUserAgentModal";
  import JInput from '@/components/jeecg/JInput'
  import UserModalDls from "./modules/UserModalDls";
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import CustomerSalesDiscountModal from './modules/CustomerSalesDiscountModal'
  import TerminalSalesDiscountModal from "./modules/TerminalSalesDiscountModal"

  export default {
    name: "UserList",
    mixins: [JeecgListMixin],
    components: {
      UserModalDls,
      SysUserAgentModal,
      UserModal,
      PasswordModal,
      JInput,
      JTreeSelect,
      getAction,
      CustomerSalesDiscountModal,
      TerminalSalesDiscountModal
    },
    data() {
      return {
        description: '这是用户管理页面',
        queryParam: {},
        userData:[],
        qrcodeData:[],
        modal: {
          title: '二维码打印',
          visible: false,
          width: '18%',
          style: { top: '20px' },
          fullScreen: true
        },
        columns: [
          {
            title: '用户账号',
            align: "center",
            dataIndex: 'username',
            width: 100
          },
          {
            title: '店铺名称',
            align: "center",
            width: 200,
            dataIndex: 'userCompany'
          },
          {
            title: '联系人',
            align:"center",
            dataIndex: 'realname',
            width: 80
          },
          {
            title: '联系电话',
            align:"center",
            dataIndex: 'phone',
            width: 100
          },
          {
            title: '店铺地址',
            align:"center",
            dataIndex: 'storeAddress',
            width: 250
          },
          {
            title: '开卡数量',
            align:"center",
            dataIndex: 'post',
            width: 80
          },
          {
            title: '推广员',
            align:"center",
            dataIndex: 'createById_dictText',
            width: 80
          },
          {
            title: '推广时间',
            align: "center",
            width: 150,
            dataIndex: 'createTime',
            sorter: true
          },
          {
            title: '操作',
            dataIndex: 'action',
            scopedSlots: {customRender: 'action'},
            align: "center",
            width: 80
          }

        ],
        url: {
          imgerver: window._CONFIG['domianURL'] + "/sys/common/view",
          syncUser: "/process/extActProcess/doSyncUser",
          list: "/customermanagement/customerManagement/storeList",
          delete: "/sys/user/delete",
          deleteBatch: "/sys/user/deleteBatch",
          exportXlsUrl: "/sys/user/exportXls",
          importExcelUrl: "sys/user/importExcel",
          getUserIdByTokenUrl:"sys/user/getUserId"
        },
      }
    },
    //加载页面时触发的
    mounted:function(){
      this.getUserId();//查询当前登陆的账号
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
    },
    computed: {
      importExcelUrl: function(){

        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      getUserId(){
        let params = {}
        //获取当前登陆用户的信息
        getAction(this.url.getUserIdByTokenUrl, params).then((res) => {
          if (res.success) {
            this.pidValue=res.result;
            console.log(this.pidValue)
          }
        });
      },
      handleUserModalDls: function () {
        this.$refs.UserModalDls.add();
        this.$refs.UserModalDls.title = "添加用户";
        this.$refs.UserModalDls.disableSubmit = false;
      },
      viewQrcode: function (record) {
        getAction('/customermanagement/customerManagement/getQRcodeById', {id:record.id}).then((res) => {
          console.log(res)
          if (res.success) {
            this.qrcodeData = res.result;
            this.modal.visible=true;
          }
        })
      },
      customerSalesConfig: function (record) {
        this.$refs.customerSalesModalForm.edit(record);
        this.$refs.customerSalesModalForm.title = "销售折扣配置";
        this.$refs.customerSalesModalForm.disableSubmit = false;

      },
      terminalSalesConfig: function (record) {
        this.$refs.terminalSalesModalForm.edit(record);
        this.$refs.terminalSalesModalForm.title = "终端折扣配置";
        this.$refs.terminalSalesModalForm.disableSubmit = false;

      },
      getAvatarView: function (avatar) {
        return this.url.imgerver + "/" + avatar;
      },

      batchFrozen: function (status) {
        if (this.selectedRowKeys.length <= 0) {
          this.$message.warning('请选择一条记录！');
          return false;
        } else {
          let ids = "";
          let that = this;
          let isAdmin = false;
          that.selectionRows.forEach(function (row) {
            if (row.username == 'admin') {
              isAdmin = true;
            }
          });
          if (isAdmin) {
            that.$message.warning('管理员账号不允许此操作,请重新选择！');
            return;
          }
          that.selectedRowKeys.forEach(function (val) {
            ids += val + ",";
          });
          that.$confirm({
            title: "确认操作",
            content: "是否" + (status == 1 ? "解冻" : "冻结") + "选中账号?",
            onOk: function () {
              frozenBatch({ids: ids, status: status}).then((res) => {
                if (res.success) {
                  that.$message.success(res.message);
                  that.loadData();
                  that.onClearSelected();
                } else {
                  that.$message.warning(res.message);
                }
              });
            }
          });
        }
      },
      handleMenuClick(e) {
        if (e.key == 1) {
          this.batchDel();
        } else if (e.key == 2) {
          this.batchFrozen(2);
        } else if (e.key == 3) {
          this.batchFrozen(1);
        }
      },
      handleFrozen: function (id, status, username) {
        let that = this;
        //TODO 后台校验管理员角色
        if ('admin' == username) {
          that.$message.warning('管理员账号不允许此操作！');
          return;
        }
        frozenBatch({ids: id, status: status}).then((res) => {
          if (res.success) {
            that.$message.success(res.message);
            that.loadData();
          } else {
            that.$message.warning(res.message);
          }
        });
      },
      handleChangePassword(username) {
        this.$refs.passwordmodal.show(username);
      },
      handleAgentSettings(username){
        this.$refs.sysUserAgentModal.agentSettings(username);
        this.$refs.sysUserAgentModal.title = "用户代理人设置";
      },
      passwordModalOk() {
        //TODO 密码修改完成 不需要刷新页面，可以把datasource中的数据更新一下
      },
      handleClickToggleFullScreen() {
        let mode = !this.modal.fullScreen
        if (mode) {
          this.modal.width = '100%'
          this.modal.style.top = '20px'
        } else {
          this.modal.width = '1200px'
          this.modal.style.top = '50px'
        }
        this.modal.fullScreen = mode
      },
    }

  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>
