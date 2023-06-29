<template>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline" @keyup.enter.native="searchQuery">
          <a-row :gutter="24">
            <a-col :md="6" :sm="6">
              <a-form-item label="用户账号">
                <a-input placeholder="用户账号" v-model="queryParam.cus" allow-clear/>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="6">
              <a-form-item label="佣金分配方式">
                <a-select placeholder="请选择" v-model="queryParam.profitType" allow-clear>
                  <a-select-option value="1">阶梯返佣</a-select-option>
                  <a-select-option value="2">长期返佣</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
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
          :scroll="{ x: 2400,}"

          @change="handleTableChange"/>
      </div>
    </a-card>
</template>

<script>
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
    export default {
        name: "ElectronChannelAgentProfitList",
        mixins: [JeecgListMixin],
        data() {
          return {
            queryParam: {},
            url: {
              list: "/electronchannelagentprofit/electronChannelAgentProfit/list",
            },
            columns: [
              {
                title:'用户账号',
                align:"center",
                dataIndex: 'username',
                scopedSlots: {customRender: 'idno'},
                width: 200
              },
              {
                title:'通道名称',
                align:"center",
                dataIndex: 'agentName',
                width: 200
              },
              {
                title:'佣金分配方式',
                align:"center",
                dataIndex: 'profitState',
                width: 250
              },
              {
                title:'激活数量开始区间',
                align:"center",
                dataIndex: 'countBegin',
                width: 250,
                customRender: function (text) {
                  if (text === null) {
                    return "-"
                  } else {
                    return text
                  }
                }
              },
              {
                title:'激活数量结束区间',
                align:"center",
                dataIndex: 'countEnd',
                width: 250,
                customRender: function (text) {
                  if (text === null) {
                    return "-"
                  } else {
                    return text
                  }
                }
              },
              {
                title:'返佣金额',
                align:"center",
                dataIndex: 'profit',
                width: 250,
                customRender: function (text) {
                  if (text === null) {
                    return "-"
                  } else {
                    return text
                  }
                }
              },
              {
                title:'备注',
                align:"center",
                dataIndex: 'remark',
                width: 250
              },
            ],
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