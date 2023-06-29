<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-spin :spinning="confirmLoading">
      <bar-multid :sourceData="cardData" :fields="cardFields" title="卡激活" :height="300"/>
    </a-spin>
    <a-spin :spinning="confirmLoading">
      <pie title="饼图" :dataSource="cardPieData"  :height="300"/>
    </a-spin>
  </a-modal>
</template>

<script>

  import BarMultid from '@/components/chart/BarMultid'
  import { getAction } from '@/api/manage'
  import Pie from '@/components/chart/Pie2'

  // const cardData = [
  //   { type: '房管', '1月': 900, '2月': 1120, '3月': 1380, '4月': 1480, '5月': 1450, '6月': 1100, '7月':1300, '8月':900,'9月':1000 ,'10月':1200 ,'11月':600 ,'12月':900 },
  //   { type: '不动产', '1月':2000, '2月': 1430, '3月': 1300, '4月': 1400, '5月': 900, '6月': 500, '7月':600, '8月':1000,'9月':600 ,'10月':1000 ,'11月':1500 ,'12月':1200 }
  // ]
  //
  // const cardFields=[
  //   '1月','2月','3月','4月','5月','6月',
  //   '7月','8月','9月','10月','11月','12月'
  // ]



  export default {
    name: "ElectronGraphReportModal",
    components: {
      BarMultid,Pie
    },
    data () {
      return {
        title:"",
        visible: false,
        model: {},
        cardFields:[],
        cardData:[],
        cardPieData:[],
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },

        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
        },
        url: "/electronchannelorder/electronChannelOrder/graphReport",
      }
    },
    created () {
    },
    methods: {
      add () {
        this.visible = true;
        getAction(this.url).then((res)=>{
          if(res.success){
            this.cardData = res.result.cardData
            this.cardFields = res.result.cardFields
            this.cardPieData= res.result.cardPieData
          }
        });
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.close()
      },
      handleCancel () {
        this.close()
      },


    }
  }
</script>

<style lang="less" scoped>

</style>