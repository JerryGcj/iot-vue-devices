<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="4" :sm="6">
            <a-form-item label="用户账号">
              <a-input placeholder="用户账号" v-model="queryParam.username"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="企业名称">
              <a-input placeholder="企业名称" v-model="queryParam.userCompany"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="联系人">
              <a-input placeholder="联系人" v-model="queryParam.realname"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="联系电话">
              <a-input placeholder="联系电话" v-model="queryParam.phone"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6">
            <a-form-item label="创建人">
              <a-input placeholder="创建人" v-model="queryParam.createBy"></a-input>
            </a-form-item>
          </a-col>
          <!--<a-col v-has="'electonChannelOrderList:selectCustomerId'" :md="5" :sm="6">
            <a-form-item label="推广员" >
              <j-tree-select
                v-model="queryParam.createById"
                placeholder="请选择推广员"
                dict="sys_user,user_company,id"
                condition='{"user_type":"4"}'
                pidField="create_by_id"
                pidValue="ae95dbad4e2e444bb6a1fce9582aac5f"/>
            </a-form-item>
          </a-col>-->
          <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button type="primary" icon="download" @click="handleExportXls('电渠代理商')" style="margin-left: 8px">导出</a-button>
              <!--<a-button v-has="'promote:add'" type="primary" @click="handleAdd" icon="plus" style="margin-left: 8px">添加店铺</a-button>-->
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
          <div class="no-print" style="text-align: right">
            <a-button v-print="'#printContent'" ghost type="primary">打印</a-button>
          </div>
        </div>
      </template>

      <section ref="print" id="printContent" class="abcdefg">
        <template v-for="(i,k) of qrcodeData"  >
<!--          <img style="width: 100%;" :src="i.storeQrcode"  >-->
          <div style="width:200px;height:220px;margin-right: 10px;margin: 0 8px 8px 0;display: inline-block">
            <div style="width: 110%;height: 110%;position: relative;padding: 8px;text-align: center;">
              <span>{{i.agentName}}</span>
              <img style="width: 100%;" :src="i.storeQrcode">
            </div>
          </div>
        </template>
      </section>
    </a-modal>

    <!-- table区域-begin -->
    <div>
      <!--<div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i>已选择&nbsp;<a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项&nbsp;&nbsp;
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>-->

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

        <span slot="cardContent" slot-scope="text">
          <j-ellipsis :value="text" :length="8" />
        </span>

        <template slot="avatarslot" slot-scope="text, record, index">
          <div class="anty-img-wrap">
            <a-avatar shape="square" :src="getAvatarView(record.avatar)" icon="user"/>
          </div>
        </template>

        <template slot="smsNotice" slot-scope="text, record, index">
          <a-switch checkedChildren="是" unCheckedChildren="否" v-model="record.smsNotice" @change="noticeChange" @click="noticeSwitch(record.id)" />
        </template>

        <span slot="action" slot-scope="text, record">
          <a v-has="'electron:getQrCodeUrl'" @click="getQrCodeUrl(record)">生成二维码</a>
          <a v-has="'electron:viewQrcode'" @click="viewQrcode(record)">查看二维码</a>
          <a-divider type="vertical"/>
          <a v-has="'electron:configChannel'" @click="configChannel(record)">配产品<a-divider type="vertical"/></a>
          <a v-has="'electron:configChannelDelete'" @click="delChannel(record)">删除产品<a-divider type="vertical"/></a>
          <a-dropdown>
            <a class="ant-dropdown-link">
              更多 <a-icon type="down"/>
            </a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a href="javascript:;" @click="handleChangePassword(record.username)">重置密码</a>
              </a-menu-item>
              <a-menu-item v-if="userId === 'ae95dbad4e2e444bb6a1fce9582aac5f' || userId === '6469a4135f239e68058d92dc894d9935'">
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a v-has="'user:delete'">删除</a>
                </a-popconfirm>
              </a-menu-item>
              <a-menu-item>
                <a  v-has="'user:changeProfit'" @click="handleChangeProfit(record)">佣金设置</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确认删除该代理商及该名下子代理商公司信息?" @confirm="handleDelCompanyInformation(record.id)">
                <a v-has="'user:deleteInfo'">删除信息</a>
              </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>


      </a-table>
    </div>
    <!-- table区域-end -->

    <user-modal ref="modalForm" @ok="modalFormOk"></user-modal>
    <password-modal ref="passwordmodal" @ok="passwordModalOk"></password-modal>
    <electron-qr-code-modal ref="qrCodeModal"></electron-qr-code-modal>
    <config-channel-modal ref="configchannelmodal" @ok="modalFormOk"></config-channel-modal>
    <del-channel-modal ref="delChannelmodal" @ok="modalFormOk"></del-channel-modal>
    <voice-modal ref="voicemodal"></voice-modal>
<!--    <profit-molal ref="profitmodal"></profit-molal>-->
    <agent-molal ref="agentmodal"/>
  </a-card>
</template>

<script>
  import JEllipsis from "@/components/jeecg/JEllipsis";
  // import ProfitMolal from './modules/ProfitMolal'
  import AgentMolal from './modules/AgentMolal'
  import ElectronQrCodeModal from './modules/ElectronQrCodeModal'
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
  import ConfigChannelModal from "./modules/ConfigChannelModal";
  import DelChannelModal from "./modules/DelChannelModal";
  import VoiceModal from "./modules/VoiceModal"
  import {postAction} from "../../api/manage";
  import store from '@/store'

  export default {
    name: "UserList",
    mixins: [JeecgListMixin],
    components: {
      // ProfitMolal,
      AgentMolal,
      ElectronQrCodeModal,
      UserModalDls,
      SysUserAgentModal,
      UserModal,
      PasswordModal,
      JInput,
      JTreeSelect,
      JEllipsis,
      getAction,
      CustomerSalesDiscountModal,
      TerminalSalesDiscountModal,
      ConfigChannelModal,
      DelChannelModal,
      VoiceModal
    },
    data() {
      return {
        userId: '',
        description: '这是用户管理页面',
        queryParam: {},
        userData:[],
        qrcodeData:[],
        imgUrl:'',
        noticeChecked: "",
        modal: {
          title: '二维码打印',
          visible: false,
          width: '50%',
          style: { top: '50px' },
          fullScreen: true
        },
        columns: [],
        columns1: [
          {
            title: '用户账号',
            align: "center",
            dataIndex: 'username',
            width: 100
          },
          {
            title: '企业名称',
            align: "center",
            width: 150,
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
            title: '备注',
            align:"center",
            dataIndex: 'note',
            width: 150
          },
          {
            title: '开卡数量',
            align:"center",
            dataIndex: 'post',
            width: 80
          },
          {
            title: '当前销售品',
            align:"center",
            dataIndex: 'qrCodeType',
            scopedSlots: {customRender: 'cardContent'},
            width: 100
          },
          {
            title: '短信通知',
            align:"center",
            width: 60,
            scopedSlots: { customRender: 'smsNotice' }
          },
          {
            title: '创建人',
            align:"center",
            dataIndex: 'createById_dictText',
            width: 80
          },
          /*{
            title: '通道',
            align:"center",
            dataIndex: 'channelId_dictText',
            width: 80
          },*/
          {
            title: '推广时间',
            align: "center",
            width: 130,
            dataIndex: 'createTime',
            sorter: true
          },
          {
            title: '操作',
            dataIndex: 'action',
            scopedSlots: {customRender: 'action'},
            align: "center",
            width: 120
          }
        ],
        columns2: [
          {
            title: '用户账号',
            align: "center",
            dataIndex: 'username',
            width: 100
          },
          {
            title: '企业名称',
            align: "center",
            width: 150,
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
            title: '备注',
            align:"center",
            dataIndex: 'note',
            width: 150
          },
          {
            title: '开卡数量',
            align:"center",
            dataIndex: 'post',
            width: 80
          },
          {
            title: '当前销售品',
            align:"center",
            dataIndex: 'qrCodeType',
            scopedSlots: {customRender: 'cardContent'},
            width: 100
          },
          {
            title: '创建人',
            align:"center",
            dataIndex: 'createById_dictText',
            width: 80
          },
          {
            title: '推广时间',
            align: "center",
            width: 130,
            dataIndex: 'createTime',
            sorter: true
          },
          {
            title: '操作',
            dataIndex: 'action',
            scopedSlots: {customRender: 'action'},
            align: "center",
            width: 120
          }

        ],
        url: {
          imgerver: window._CONFIG['domianURL'] + "/sys/common/view",
          syncUser: "/process/extActProcess/doSyncUser",
          list: "/sys/telecomAgent/list",
          delete: "/sys/user/delete",
          deleteBatch: "/sys/user/deleteBatch",
          exportXlsUrl: "/sys/telecomAgent/exportXls",
          importExcelUrl: "sys/user/importExcel",
          getUserIdByTokenUrl:"sys/user/getUserId",
          getQrcode:"/sys/telecomAgent/generaQrCode",
          noticeSwitchUrl: "/sys/user/noticeSwitch",
          cusIdSaveRedis: "/sys/telecomAgent/cusIdSaveRedis"
        },
      }
    },
    //加载页面时触发的
    mounted:function(){
      // this.getUserId();//查询当前登陆的账号
      // var that = this;
      // queryLowerAgent().then((res)=>{
      //   if(res.success){
      //     that.userData = [];
      //     let treeList = res.result
      //     for(let a=0;a<treeList.length;a++){
      //       let temp = treeList[a];
      //       this.userData.push({
      //         value:temp.id,
      //         text:temp.userCompany
      //       })
      //     }
      //   }
      // });

    },
    created() {
      getAction('/sys/user/queryUser').then((res) => {
        console.log(res)
        if (res.success) {
          this.initColumns(res.result.username);
        }
      });
      this.getUserId();

    },
    computed: {
      importExcelUrl: function(){

        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      getQrCodeUrl: function (record) {
        /*this.$refs.qrCodeModal.visible = true;
        this.$refs.qrCodeModal.title = "电渠代理商"+record.realname+"二维码";
        this.$refs.qrCodeModal.show(record.id);*/
        this.$refs.voicemodal.visible = true;
        this.$refs.voicemodal.title = "生成二维码";
        this.$refs.voicemodal.show(record);
      },
      getUserId(){
        let params = {}
        //获取当前登陆用户的信息
        getAction(this.url.getUserIdByTokenUrl, params).then((res) => {
          if (res.success) {
            this.userId=res.result.id;
            console.log("========>" + this.userId)
          }
        });
      },
      handleUserModalDls: function () {
        this.$refs.UserModalDls.add();
        this.$refs.UserModalDls.title = "添加用户";
        this.$refs.UserModalDls.disableSubmit = false;
      },
      viewQrcode: function (record) {
        getAction(this.url.getQrcode+"/"+record.id+"/2", null).then((res) => {
          console.log(res)
          if (res.success) {
            this.modal.title = "电渠代理商【"+res.result[0].realname+"】二维码";
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
        handleChangeProfit(record) {
        this.$refs.agentmodal.show(record);
      },
        handleDelCompanyInformation(cusId) {
          let param = {cusId:cusId};
          postAction(this.url.cusIdSaveRedis,param).then((res) => {
              if (res.success) {
                  this.$message.success(res.message);
              } else {
                  this.$message.warning(res.message);
              }
          })
        },
      configChannel(record) {
        this.$refs.configchannelmodal.edit(record);
        this.$refs.configchannelmodal.title = "配置通道";
        this.$refs.configchannelmodal.disableSubmit = false;
      },
      delChannel(record) {
        this.$refs.delChannelmodal.edit(record);
        // this.$refs.delChannelmodal.title = "产品删除";
        this.$refs.delChannelmodal.disableSubmit = false;
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
      noticeChange(checked) {
        this.noticeChecked = checked;
      },
      noticeSwitch(id){
        const that = this;
        putAction(this.url.noticeSwitchUrl,{noticeChecked: this.noticeChecked,id: id}).then((res)=>{
          if(res.success){
            that.$message.success(res.message);
            that.$emit('ok');
            this.initData();
          }else{
            that.$message.warning(res.message);
          }
        });
      },
      initColumns(userName){
        console.log('userName='+userName)
        //权限过滤（列权限控制时打开，修改第二个参数为授权码前缀）
        if(userName=='admin'||userName=='qianxun'||userName=='xszczg002'||userName=='xszczg001'||userName=='xszc006'){
          this.columns = this.columns1;
        }else{
          this.columns = this.columns2;
        }
      }
    }

  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>
