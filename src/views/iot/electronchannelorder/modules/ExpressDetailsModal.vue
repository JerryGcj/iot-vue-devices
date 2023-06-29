<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    :okButtonProps="{ props: {disabled: disableSubmit} }"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-card :bordered="false">
      <div slot="title"><a-icon type="smile" />单号：{{expressNo}}</div>
      <div slot="title"><a-icon type="smile" />快递：{{expressCompany}}</div>
      <ul class="time-axis"  :class="{ 'is-done': index === 0 }" v-for="(item,index) in timeAxis" :key="index">
        <li>{{ item.ftime }}</li>
        <li>{{ item.context }}</li>
      </ul>
      <!--<div class='time-line'>
        <div v-for='item in timeAxis' class='time-line-div'>
          <p>{{item.ftime}}</p>
          <p ref='circular'></p>
          <p>{{item.context}}</p>
        </div>
        <div class='img-dotted' ref='dotted'></div>
      </div>-->
    </a-card>
  </a-modal>
</template>

<script>

  export default {
    name: "CardDistributionModal",
    data () {
      return {
        importBatch:'',
        title:"",
        visible: false,
        model: {},
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
        // 展示时间轴的详情列表
        timeAxis: [],
        expressNo:'',
        expressCompany:''
      }
    },
    created () {
    },
    methods: {
      add (record) {
        this.expressNo = record.expressNo;
        this.expressCompany = record.expressCompany;
        this.timeAxis = record.details;
        this.visible = true;
      },

      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.$emit('close');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      },


    }
  }
</script>

<style lang="less" scoped>
  /* 时间轴 */
  .time-axis {
    color: #262626;
    position: relative;
    padding-left: 30px;
    list-style: none;
    &:before {
      position: absolute;
      margin-top: 8px;
      left: 4px;
      content: " ";
      height: 8px;
      width: 8px;
      border-radius: 50%;
      background-color: #e8e8e8;
    }
    &:after {
      position: absolute;
      top: 16px;
      left: 8px;
      bottom: -19px;
      content: " ";
      width: 1px;
      border-right: 1px solid #e8e8e8;
    }
    &:not(:first-child) {
      padding-top: 8px;
    }
    &:last-child {
      &:after {
        display: none;
      }
    }
    &.is-done {
      &:after {
        border-color: #0091fa;
      }
      &:before {
        background-color: #1874ff;
        box-shadow: #1874ff 0 0 10px;
      }
    }
  }
  .time h2{
    color:#FF6600;
    margin: 20px auto 30px auto;
  }
  .time-line{
    position: relative;
    width:600px;
    margin:0 auto;
  }
  .time-line-div{
    position:relative;
    min-height:120px;
  }
  .time-line-div>p:nth-child(1){
    position: absolute;
    left:0;
    width:100px;
  }
  .time-line-div>p:nth-child(2){
    position:absolute;
    left: 100px;
    width:15px;
    height:15px;
    top:10px;
    background:#5CB85C;
    border-radius: 50%;
    z-index: 10
  }
  .time-line-div>p:nth-child(3){
    position:absolute;
    left: 130px;
    padding: 10px;
    width:450px;
    background: #317EF3;
    font-size:.8rem;
    color:#fff;
    border-radius: 10px;
  }
  .img-dotted{
    position:absolute;
    width:20px;
    height:100%;
    top:0;
    z-index: 1;
    background:url('/static/images/vue/dotted.png');
  }
  .time-line-detail>p{
    margin-left: 30px;
    margin-bottom: 10px;
  }
</style>