<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="运营商">
              <a-select
                allow-clear
                v-model="queryParam.id"
                mode="result"
                style="width: 100%"
                placeholder="请选择"
                :options="dictOperatorOptions"
                :autoClearSearchValue="false"
                :maxTagTextLength=3
                :maxTagCount=3
              ></a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="通道简称缩写">
              <a-input placeholder="请输入通道简称缩写" v-model="queryParam.agentSimpleName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="通道名称">
              <a-input placeholder="请输入通道名称" v-model="queryParam.agentName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="套餐名称">
              <a-input placeholder="请输入套餐名称" v-model="queryParam.packageName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="通道状态">
              <a-select placeholder="请选择" v-model="queryParam.delFlag" allow-clear>
                <a-select-option value="0">上架</a-select-option>
                <a-select-option value="1">下架</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="返佣条件">
              <a-select placeholder="请选择" v-model="queryParam.rakeBackTag" allow-clear>
                <a-select-option value="0">不返</a-select-option>
                <a-select-option value="1">按首充</a-select-option>
                <a-select-option value="2">按激活</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="6" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
            </span>
          </a-col>

        </a-row>
      </a-form>
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
        :scroll="{ x: 2400}"
        :pagination="ipagination"
        :loading="loading"
        @change="handleTableChange">


        <span slot="action" slot-scope="text, record">
          <a v-has="'channelAgent:edit'" @click="handleEdit(record)">编辑<a-divider type="vertical" /></a>
          <a v-has="'channelAgent:users'" @click="handleOpen(record)">用户<a-divider type="vertical" /></a>
          <a @click="handleImport(record)">上传图片</a>
          <a-dropdown>
            <a v-has="'channelAgent:more'" class="ant-dropdown-link">
              <a-divider type="vertical" />更多 <a-icon type="down"/>
            </a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                <a >删除</a>
                </a-popconfirm>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定上架吗?" @confirm="() => upAgent(record)">
                  <a >上架</a>
                </a-popconfirm>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定下架吗?" @confirm="() => downAgent(record)">
                  <a >下架</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
      <!-- 抽屉弹框 -->
      <a-drawer
        :width="800"
        placement="right"
        :closable="true"
        :maskClosable="false"
        :visible="visible"
        @close="handleClose">

        <!-- 千寻子代理查询-->
        <div class="table-page-search-wrapper">
          <a-form layout="inline">
            <a-row :gutter="24">
              <a-col :md="12" :sm="12">
                <a-form-item label="用户账号">
                  <a-input placeholder="请输入用户账号" v-model="queryParam2.userName" allow-clear></a-input>
                </a-form-item>
              </a-col>
              <a-col :md="10" :sm="10">
                <a-form-item label="状态">
                  <a-select v-model="queryParam2.state" placeholder="请选择" allow-clear>
                    <a-select-option value=0>上架</a-select-option>
                    <a-select-option value=1>下架</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
              <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
            <a-col :md="9" :sm="24">
             <a-button type="primary"  @click="searchQuery2" icon="search" style="margin-left: 21px">查询</a-button>
             <a-button type="primary" @click="searchReset2" icon="reload" style="margin-left: 8px">重置</a-button>
             <a-button type="primary" @click="upAgent2('all')" icon="reload" style="margin-left: 8px">一键上架</a-button>
             <a-button type="primary" @click="downAgent2('all')" icon="reload" style="margin-left: 8px">一键下架</a-button>
            </a-col>
          </span>
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
            :loading="loading2"
            :rowSelection="{selectedRowKeys: selectedRowKeys2, onChange: onSelectChange2}"
            @change="handleTableChange2">
            <span slot="action" slot-scope="text, record">
              <a  v-if="record.subagentState !== '暂无子代理'" @click="handleOpen2(record)">用户<a-divider type="vertical" /></a>
              <a-popconfirm title="确定上架吗?" @confirm="() => upAgent2(record)">
                <a v-if="record.agentState == '下架'">上架</a>
              </a-popconfirm>
              <a-popconfirm title="确定下架吗?" @confirm="() => downAgent2(record)">
                <a v-if="record.agentState == '上架'">下架</a>
              </a-popconfirm>
            </span>
          </a-table>
        </div>



      </a-drawer>
      <a-drawer
        :width="800"
        placement="right"
        :closable="true"
        :maskClosable="false"
        :visible="visible2"
        @close="handleClose2">
        <div class="table-page-search-wrapper">
          <a-form layout="inline">
            <a-row :gutter="24">
              <a-col :md="9" :sm="24">
                <a-button v-if="this.secondaryTag == '下架'" type="primary"  @click="upAgent2" icon="search" style="margin-left: 21px">一键上架</a-button>
                <a-button v-if="this.secondaryTag == '上架'" type="primary" @click="downAgent2" icon="reload" style="margin-left: 8px">一键下架</a-button>
              </a-col>
            </a-row>
          </a-form>
        </div>
        <a-table
          style="height:500px"
          ref="table2"
          bordered
          size="middle"
          rowKey="id"
          :columns="columns3"
          :dataSource="dataSource3"
          :pagination="ipagination3"
          :loading="loading3"
          :rowSelection="{selectedRowKeys: selectedRowKeys3, onChange: onSelectChange3}"
          @change="handleTableChange3"/>
      </a-drawer>

    </div>
    <ElectronChannelAgentEditModal ref="electronChannelAgentEditModal"  @ok="modalFormOk"/>
    <electron-order-import-picture-modal ref="importPictureModal" @ok="modalFormOk"></electron-order-import-picture-modal>
  </a-card>
</template>

<script>
    import JEllipsis from "@/components/jeecg/JEllipsis";
    import JTreeSelect from '@/components/jeecg/JTreeSelect'
    import { JeecgListMixin } from '@/mixins/JeecgListMixin'
    import { postAction,getAction,downFile,httpAction} from '@/api/manage'
    import JDate from '@/components/jeecg/JDate'
    import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
    import JMultiSelectTag from '@/components/dict/JMultiSelectTag'
    import ElectronChannelAgentEditModal from './modules/ElectronChannelAgentEditModal'
    import { filterObj } from '@/utils/util'
    import ElectronOrderImportPictureModal from './modules/ElectronOrderImportPictureModal'

    export default {
        name: "ElectronChannelAgentList",
        mixins:[JeecgListMixin],
        components: {
            JTreeSelect,
            JEllipsis,
            JDate,
            JSearchSelectTag,
            JMultiSelectTag,
            ElectronChannelAgentEditModal,
            ElectronOrderImportPictureModal
        },
        data () {
            return {
              secondLevelUserName:"",
              secondaryTag:"",
              state2: "",
              secondaryAgent: "",
              isorter2: {
                column: 'createTime',
                order: 'desc'
              },
              isorter3: {
                column: 'createTime',
                order: 'desc'
              },
              filters2: {},
              selectedRowKeys2: [],
              selectedRowKeys3: [],
              selectionRows3: [],
              loading2: false,
              loading3: false,
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
              ipagination3: {
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
              dataSource2:[],
              dataSource3:[],
              queryParam2: {
                userName: "",
                state:undefined
              },
              visible: false,
              visible2: false,
              dictOperatorOptions:[],
              columns2: [
                {
                  title: '用户账号',
                  align: 'center',
                  dataIndex: 'username',
                  width: 120
                },
                {
                  title: '用户名称',
                  align: 'center',
                  width: 100,
                  dataIndex: 'realname'
                },
                {
                  title: '状态',
                  align: 'center',
                  width: 80,
                  dataIndex: 'agentState'
                },
                {
                  title: '子代理状态',
                  align: 'center',
                  width: 80,
                  dataIndex: 'subagentState'
                },
                {
                  title: '操作',
                  dataIndex: 'action',
                  scopedSlots: { customRender: 'action' },
                  align: 'center',
                  width: 120
                }
              ],
              columns3: [
                {
                  title: '用户账号',
                  align: 'center',
                  dataIndex: 'username',
                  width: 120
                },
                {
                  title: '用户名称',
                  align: 'center',
                  width: 100,
                  dataIndex: 'realname'
                },
                {
                  title: '状态',
                  align: 'center',
                  width: 80,
                  dataIndex: 'agentState'
                },
              ],
                // 表头
                columns: [
                    {
                        title:'id',
                        align:"center",
                        dataIndex: 'id',
                        width: 200
                    },
                    {
                        title:'通道简称缩写',
                        align:"center",
                        dataIndex: 'agentSimpleName',
                        width: 200
                    },
                    {
                        title:'通道名称',
                        align:"center",
                        dataIndex: 'agentName',
                        width: 200
                    },
                    {
                        title:'套餐名称',
                        align:"center",
                        dataIndex: 'packageName',
                        scopedSlots: {customRender: 'esContent2'},
                        width: 200
                    },
                    {
                        title:'宣传资费',
                        align:"center",
                        dataIndex: 'expenseExplain',
                        width: 200
                    },
                    {
                        title:'套餐编码',
                        align:"center",
                        dataIndex: 'packageCode',
                        width: 200
                    },
                  {
                    title:'通道状态',
                    align:"center",
                    dataIndex: 'delFlag',
                    width: 200
                  },
                  {
                    title:'佣金条件',
                    align:"center",
                    dataIndex: 'rakeBackTag',
                    width: 200,
                    customRender: function (text) {
                      if (text === "0") {
                        return "不返"
                      } else if (text === "1") {
                        return "按首充"
                      } else if (text === "2") {
                        return "按激活"
                      }
                    }
                  },
                  {
                    title:'备注',
                    align:"center",
                    dataIndex: 'remark',
                    width: 200
                  },
                  {
                    title:'通道图片',
                    align:"center",
                    dataIndex: 'thumbnailUrl',
                    customRender: function (t,r, index) {
                      if (t !== null && t !== "" && t !== undefined) {
                        return <img src={t} style="width: 200px; height: 100px; "/>
                      }
                    }
                  },
                    {
                        title: '操作',
                        dataIndex: 'action',
                        align:"center",
                        scopedSlots: { customRender: 'action' },
                        width: 300
                    }
                ],
                url: {
                    agentEdit: "/electronchannelagent/electronChannelAgent/agentEdit",
                    upOrDownAgent: "/electronchannelagent/electronChannelAgentDel/upOrDownAgent",
                    list2: "/electronchannelagent/electronChannelAgent/getSecondLevelUser",
                    list3: "/electronchannelagent/electronChannelAgent/getSubagentUser",
                    list: "/electronchannelagent/electronChannelAgent/list",
                    delete: "/electronchannelagent/electronChannelAgent/delete",
                    initOperatorUrl: "/electronchannelagent/electronChannelAgent/initOperator",
                },
            }
        },
        created () {
            this.operatorValue =[];
            this.initOperator();
        },
        methods: {
          //二级代理及以下代理通道上架
          upAgent2(record) {
            var data = {
              tag: "",
              state: "",
              agentId: ""
            };
            if (record == "all") {
              data.tag = "all";
              data.state = "0"
              data.agentId = this.agentId
              this.editAgent2(data)
            } else {
              record.state = "0"
              record.tag = "tag"
              this.editAgent2(record)
            }
          },
          //二级代理及以下代理通道下架
          downAgent2(record) {
            var data = {
              tag: "",
              state: "",
              agentId: ""
            };
            if (record == "all") {
              data.tag = "all";
              data.state = "1"
              data.agentId = this.agentId
              this.editAgent2(data)
            } else {
              record.state = "1"
              record.tag = "tag"
              this.editAgent2(record)
            }
          },
          //二级代理及以下代理通道上架 下架
          editAgent2(record) {
            const formData = new FormData();
            //所有用户一键上下架
            if (record.tag == "all") {
              formData.append("tag","all");
            }
            //子代理一键上下架
            if (this.secondaryAgent != "") {
              formData.append("cusId",this.secondaryAgent)
              formData.append("tag","tag");
            } else {
              //二级代理上下架
              formData.append("cusId",record.id);
            }
            formData.append("state",record.state);
            formData.append("agentId",this.agentId);

            httpAction(this.url.upOrDownAgent, formData, 'put').then((res) => {
              if (res.success) {
                this.$message.success(res.message);
                this.loadData2(this.ipagination2.current);
                if (this.secondLevelUserName != "") {
                  this.loadData3(1,this.secondLevelUserName)
                }
                this.close();
              } else {
                this.message.warning(res.message);
              }
            }).finally(() => {
              this.confirmLoading = false;
            })
          },
          //通道上架
          upAgent(record) {
            record.state = "0"
            this.editAgent(record)
            console.log(record)

          },
          //通道下架
          downAgent(record) {
            record.state = "1"
            this.editAgent(record)
          },
          //通道上架 下架
          editAgent(record) {
            const formData = new FormData();
            formData.append("agentId",record.id);
            formData.append("state",record.state);
            httpAction(this.url.agentEdit, formData, 'put').then((res) => {
              if (res.success) {
                this.$message.success(res.message);
                this.loadData(1);
                this.close();
              } else {
                this.message.warning(res.message);
              }
            }).finally(() => {
              this.confirmLoading = false;
            })
          },
          //查询
          searchQuery2() {
            this.loadData2(1)
          },
          //重置
          searchReset2() {
            this.queryParam2 = {}
            this.loadData2(1)
          },
          handleTableChange2(pagination, filters, sorter) {
            //分页、排序、筛选变化时触发
            //TODO 筛选
            if (Object.keys(sorter).length > 0) {
              this.isorter2.column = sorter.field
              this.isorter2.order = 'ascend' == sorter.order ? 'asc' : 'desc'
            }
            this.ipagination2 = pagination
            this.loadData2()
          },
          onSelectChange2(selectedRowKeys, selectionRows) {
            this.selectedRowKeys2 = selectedRowKeys
            this.selectionRows2 = selectionRows
          },

          //获取查询条件
          getQueryParams2() {
            let sqp = {}
            if (this.superQueryParams2) {
              sqp['superQueryParams'] = encodeURI(this.superQueryParams2)
            }
            var param = Object.assign(sqp, this.queryParam2, this.isorter2, this.filters2)
            param.pageNo = this.ipagination2.current
            param.pageSize = this.ipagination2.pageSize
            return filterObj(param)
          },

          handleTableChange3(pagination, filters, sorter) {
            //分页、排序、筛选变化时触发
            //TODO 筛选
            if (Object.keys(sorter).length > 0) {
              this.isorter3.column = sorter.field
              this.isorter3.order = 'ascend' == sorter.order ? 'asc' : 'desc'
            }
            this.ipagination3 = pagination
            this.loadData3(pagination.current, this.secondLevelUserName)
          },
          onSelectChange3(selectedRowKeys, selectionRows) {
            this.selectedRowKeys3 = selectedRowKeys
            this.selectionRows3 = selectionRows
          },

          //获取查询条件
          getQueryParams3() {
            let sqp = {}
            if (this.superQueryParams2) {
              sqp['superQueryParams'] = encodeURI(this.superQueryParams2)
            }
            var param = Object.assign(sqp, this.queryParam2, this.isorter2, this.filters2)
            param.pageNo = this.ipagination3.current
            param.pageSize = this.ipagination3.pageSize
            return filterObj(param)
          },


          //查询千寻下子代理商
          loadData2(arg) {
            if (!this.url.list2) {
              this.$message.error('请设置url.list2属性!')
              return
            }
            //加载数据 若传入参数1则加载第一页的内容
            if (arg === 1) {
              this.ipagination2.current = 1
            }
            let params = this.getQueryParams2()//查询条件
            params.agentId = this.agentId;
            this.loading2 = true
              getAction(this.url.list2, params).then((res) => {
                if (res.success) {
                  this.dataSource2 = res.result.records
                  this.ipagination2.total = res.result.total
                }
                this.loading2 = false
              })
          },


          //查询二级代理下的子代理
          loadData3(arg,username) {
            if (!this.url.list3) {
              this.$message.error('请设置url.list3属性!')
              return
            }
            //加载数据 若传入参数1则加载第一页的内容
            if (arg === 1) {
              this.ipagination3.current = 1
            }
            let params = this.getQueryParams3()//查询条件
            params.username = username;
            params.agentId = this.agentId;
            this.loading3 = true
            getAction(this.url.list3, params).then((res) => {
              if (res.success) {
                this.dataSource3 = res.result.records
                this.ipagination3.total = res.result.total
                this.secondaryTag = res.result.records[0].agentState;
              }
              this.loading3 = false
            })
          },


          //点击用户打开页面二级代理
          handleOpen(record) {
            this.visible = true
            this.agentId = record.id
            this.loadData2();
          },

          //关闭用户页面二级代理
          handleClose() {
            this.visible  = false;
            this.agentId = ""
            this.queryParam2.userName = ""
            this.queryParam2.state = undefined
          },

          //点击用户打开页面子代理
          handleOpen2(record) {
            this.secondaryAgent = record.id;
            this.visible2 = true
            this.loadData3(1,record.username);
            this.secondLevelUserName = record.username
          },

          //关闭用户页面子代理
          handleClose2() {
            this.visible2  = false;
            this.secondaryAgent = "";
            this.loadData2(1);
            this.secondLevelUserName = ""
          },

            initOperator(){
                getAction(this.url.initOperatorUrl).then((res)=>{
                    if(res.success){
                        this.dictOperatorOptions = res.result;
                    }
                })

            },
            handleEdit(data) {
                this.$refs.electronChannelAgentEditModal.edit(data)
                this.$refs.electronChannelAgentEditModal.title = "编辑";
                this.$refs.electronChannelAgentEditModal.disableSubmit = false;
            },
          handleImport(record) {
            this.$refs.importPictureModal.show();
            this.$refs.importPictureModal.agentId = record.id;

          }
        }
    }
</script>
<style scoped lang="less">
  @import '~@assets/less/common.less';
  /deep/ .ant-calendar-picker {
  min-width: 33%;
  max-width: 40%;
}
</style>