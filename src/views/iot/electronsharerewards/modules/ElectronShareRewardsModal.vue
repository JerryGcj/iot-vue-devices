<template>
  <a-modal
    :title="title"
    :width="1200"
    :visible="visible"
    :maskClosable="false"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel">
    <a-spin :spinning="confirmLoading" id="formSpin">
      <!-- 主表单区域 -->
      <a-form :form="form">
        <a-row>
          <a-col :span="12">
            <a-form-item label="分成奖励名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['rewardsName', validatorRules.rewardsName]" placeholder="请输入分成奖励名称"/>
            </a-form-item>
          </a-col>
        </a-row>

        <a-row>
          <a-col :span="12">
            <a-form-item label="运营商" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-select v-decorator="['operatorName', validatorRules.operatorName]" placeholder="请选择运营商"
                        :allowClear="true" @change="operatorChange" style="width: 100%"
                        :getPopupContainer="getPopupContainer">
                <a-select-option value="unicom">联通</a-select-option>
                <a-select-option value="mobile">移动</a-select-option>
                <a-select-option value="telecom">电信</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
        </a-row>

        <a-row>
          <a-col :span="12">
            <a-form-item label="归属省市" :labelCol="labelCol" :wrapperCol="wrapperCol" :rules="rules">
              <a-select v-decorator="['belongArea',validatorRules.belongArea]" placeholder="请选择归属省市" id="" allowClear
                        style="width: 100%" :getPopupContainer="getPopupContainer">
                <a-select-option v-for="(item,index) in belongAreaList" :key="index" :value="item">{{
                    item
                  }}
                </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
        </a-row>

        <a-row>
          <a-col :span="12">
            <a-form-item label="生效开始日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择开始日期" v-decorator="[ 'effectiveDateStart', validatorRules.effectiveDateStart]"
                      :trigger-change="true" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="生效结束日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择结束日期" v-decorator="[ 'effectiveDateEnd', validatorRules.effectiveDateEnd]"
                      :trigger-change="true" style="width: 100%"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row>
          <a-col :span="12">
            <a-form-item label="月激活达标标准（%）" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number type="list" v-decorator="['monthStandard', validatorRules.monthStandard]"
                              placeholder="请输入月激活达标标准" style="width:100%"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row>
          <a-col :span="12">
            <a-form-item label="分成月数" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="[ 'dividedMonths', validatorRules.dividedMonths]" placeholder="请输入分成月数"
                              style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="是否展示" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-select v-decorator="['displayStatus', validatorRules.displayStatus]" placeholder="请选择是否展示" allowClear>
                <a-select-option :value="1">展示</a-select-option>
                <a-select-option :value="0">不展示</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

        </a-row>

      </a-form>

      <!-- 子表单区域 -->
      <a-tabs v-model="activeKey" @change="handleChangeTabs">
        <a-tab-pane tab=" 月发展量阶梯表" :key="refKeys[0]" :forceRender="true">
          <j-editable-table
            :ref="refKeys[0]"
            :loading="electronShareRewardsLadderTable.loading"
            :columns="electronShareRewardsLadderTable.columns"
            :dataSource="electronShareRewardsLadderTable.dataSource"
            :maxHeight="300"
            :rowNumber="true"
            :rowSelection="true"
            :actionButton="true"
          />
        </a-tab-pane>
      </a-tabs>
    </a-spin>
  </a-modal>
</template>

<script>

import pick from 'lodash.pick'
import {FormTypes, getRefPromise} from '@/utils/JEditableTableUtil'
import {JEditableTableMixin} from '@/mixins/JEditableTableMixin'
import JDate from '@/components/jeecg/JDate'
import JDictSelectTag from "@/components/dict/JDictSelectTag"
import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
import {getAction, postAction} from "@api/manage";

export default {
  name: 'ElectronShareRewardsModal',
  mixins: [JEditableTableMixin],
  components: {
    JDate,
    JDictSelectTag,
    JSearchSelectTag,
  },
  data() {
    return {
      defaultValue: undefined,
      rules: [{required: true, message: '请输入分成奖励名称!'}],
      belongAreaList: [],
      labelCol: {
        span: 6
      },
      wrapperCol: {
        span: 16
      },
      labelCol2: {
        span: 3
      },
      wrapperCol2: {
        span: 20
      },
      // 新增时子表默认添加几行空数据
      addDefaultRowNum: 1,
      validatorRules: {
        rewardsName: {rules: [{required: true, message: '请输入分成奖励名称!'}]},
        operatorName: {rules: [{required: true, message: '请选择运营商!'}]},
        // belongArea:undefined,    initialValue:''
        belongArea: {initialValue: undefined, rules: [{required: true, message: '请选择运营商!'}]},
        effectiveDateStart: {rules: [{required: true, message: '请选择开始日期!'}]},
        effectiveDateEnd: {},
        dividedMonths: {},
        displayStatus: {rules: [{required: true, message: '请选择是否展示!'}]},
        monthStandard: {rules: [{required: true, message: '请输入月激活达标标准!'}]},
      },
      refKeys: ['electronShareRewardsLadder',],
      tableKeys: ['electronShareRewardsLadder',],
      activeKey: 'electronShareRewardsLadder',
      //  分成奖励-月发展量阶梯表
      electronShareRewardsLadderTable: {
        loading: false,
        dataSource: [],
        columns: [
          {
            title: '档位名称',
            key: 'gearName',
            type: FormTypes.input,
            width: "200px",
            placeholder: '请输入${title}',
            defaultValue: '',
            // 表单验证规则
            validateRules: [
              {
                required: true, // 必填
                message: '${title}不能为空' // 提示的文本
              }
            ]
          },
          {
            title: '月发展量起始户数',
            key: 'monthNumberStart',
            type: FormTypes.inputNumber,
            width: "200px",
            placeholder: '请输入${title}',
            defaultValue: '',
            // 表单验证规则
            validateRules: [
              {
                required: true, // 必填
                message: '${title}不能为空' // 提示的文本
              }
            ]
          },
          {
            title: '月发展量截止户数',
            key: 'monthNumberEnd',
            type: FormTypes.inputNumber,
            width: "200px",
            placeholder: '请输入${title}',
            defaultValue: '',
          },
          {
            title: '分成比例（%）',
            key: 'shareProportion',
            type: FormTypes.inputNumber,
            width: "200px",
            placeholder: '请输入${title}',
            defaultValue: '',
          },
        ]
      },
      url: {
        add: "/sharerewards/electronShareRewards/add",
        edit: "/sharerewards/electronShareRewards/edit",
        electronShareRewardsLadder: {
          list: '/sharerewards/electronShareRewards/queryElectronShareRewardsLadderByMainId'
        },
      }
    }
  },
  methods: {
    getPopupContainer(node) {
      if (node) {
        return node.parentNode
      }
      return document.body
    },
    operatorChange(value) {
      let params = {operatorName: value};
      var _this = this;
      this.form.setFieldsValue({
        belongArea: undefined
      })
      getAction('/sharerewards/electronShareRewards/queryBelongAreaByOperator', params).then((res) => {

        if (res.success) {
          _this.belongAreaList = res.result;
          console.log(_this.belongAreaList)
        } else {
          this.$message.warn(res.message)
        }
      })
    },
    getAllTable() {
      let values = this.tableKeys.map(key => getRefPromise(this, key))
      return Promise.all(values)
    },
    /** 调用完edit()方法之后会自动调用此方法 */
    editAfter(record) {
      let fieldval = pick(this.model, 'createBy', 'createTime', 'rewardsName', 'operatorName', 'belongArea', 'effectiveDateStart', 'effectiveDateEnd', 'dividedMonths', 'displayStatus', 'monthStandard', 'gearName')
      this.model = Object.assign({}, record);
      this.$nextTick(() => {
        this.form.setFieldsValue(fieldval)
      })
      // 加载子表数据
      if (this.model.id) {
        let params = {id: this.model.id}
        this.requestSubTableData(this.url.electronShareRewardsLadder.list, params, this.electronShareRewardsLadderTable)
      }
    },
    /** 整理成formData */
    classifyIntoFormData(allValues) {
      let main = Object.assign(this.model, allValues.formValue)

      return {
        ...main, // 展开
        electronShareRewardsLadderList: allValues.tablesValue[0].values,
      }
    },
    validateError(msg) {
      this.$message.error(msg)
    },
    popupCallback(row) {
      this.form.setFieldsValue(pick(row, 'createBy', 'createTime', 'rewardsName', 'operatorName', 'belongArea', 'effectiveDateStart', 'effectiveDateEnd', 'dividedMonths', 'displayStatus', 'monthStandard'))
    },

  }
}
</script>

<style scoped>
</style>