<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="6">
            <a-form-item label="用户账号">
              <a-input placeholder="请输入用户账号" v-model="queryParam.userName" allowClear></a-input>
            </a-form-item>
          </a-col>
<!--          <a-col :md="6" :sm="6">-->
<!--            <a-form-item label="状态">-->
<!--              <a-select placeholder="请选择" v-model="queryParam.status" allow-clear>-->
<!--                <a-select-option value="0">上架</a-select-option>-->
<!--                <a-select-option value="1">下架</a-select-option>-->
<!--              </a-select>-->
<!--            </a-form-item>-->
<!--          </a-col>-->
<!--          <a-col :md="8" :sm="12">-->
<!--            <a-form-item label="运营商">-->
<!--              <a-select-->
<!--                allowClear="true"-->
<!--                v-model="queryParam.operatorValue"-->
<!--                mode="multiple"-->
<!--                placeholder="请选择"-->
<!--                :options="dictOperatorOptions"-->
<!--                :filterOption="likeQuery"-->
<!--                :autoClearSearchValue="false"-->
<!--                :maxTagTextLength=3-->
<!--                :maxTagCount=3-->
<!--              ></a-select>-->
<!--            </a-form-item>-->
<!--          </a-col>-->
          <a-col :md="4" :sm="6">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchQuery" icon="reload" style="margin-left: 8px">重置</a-button>
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

        @change="handleTableChange"/>
    </div>
  </a-card>
    
</template>

<script>
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import { getAction} from '@/api/manage'

    export default {
        name: "ElectronChannelAgentDelList",
        mixins:[JeecgListMixin],
        data () {
          return {
            operatorValue: [],
            dictOperatorOptions:[],
            url: {
              initOperatorUrl: "/electronchannelagent/electronChannelAgent/initOperator",
              list: "/electronchanneluser/electronChannelUser/queryRackUpAndDownByUser",
            },
            columns: [
              {
                title:'用户账号',
                align:"center",
                dataIndex: 'realname',
                scopedSlots: {customRender: 'idno'},
                width: 50
              },
              {
                title:'通道名称',
                align:"center",
                dataIndex: 'agentName',
                width: 110
              },
              {
                title:'状态',
                align:"center",
                dataIndex: 'isDel',
                width: 50
              },
            ],
          }
        },
      created() {
        this.initOperator();
      },
      methods: {
        searchQuery() {
          this.loadData(1);
        },
        likeQuery(input, option){
          return (option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0)
        },
        initOperator() {
         getAction(this.url.initOperatorUrl).then((res) => {
           if (res.success) {
             this.dictOperatorOptions = res.result
           }
         })
        },
      },
    }
</script>


<style scoped>

</style>