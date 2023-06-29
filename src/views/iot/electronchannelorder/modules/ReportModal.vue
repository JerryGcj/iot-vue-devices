<template>
  <a-modal
    :title="title"
    :width="1000"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-spin :spinning="confirmLoading">
      <a-card :bordered="false" :body-style="{padding: '0'}">
        <div class="salesCard">

          <a-tabs default-active-key="1" size="large" :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}">
            <div class="extra-wrapper" slot="tabBarExtraContent" style="width: 150px">
              <j-tree-select
                @change="handleChange"
                v-model="cusId"
                placeholder="请选择客户"
                dict="sys_user,user_company,id"
                condition='{"user_type":"4"}'
                pidField="create_by_id"
                pidValue="ae95dbad4e2e444bb6a1fce9582aac5f"/>
            </div>
            <a-tab-pane loading="true" tab="报表" key="1">
              <a-row>
                <a-col :xl="24" :lg="24" :md="24" :sm="24" :xs="24">
                  <bar title="报表(当月)" :dataSource="barData"/>
                </a-col>
              </a-row>
            </a-tab-pane>
          </a-tabs>
        </div>
      </a-card>


     <!-- <a-form>
      <a-card :bordered="false" :body-style="{padding: '0'}">
        <a-row>
        <a-col :span="5">
            <j-tree-select
              @change="handleChange"
              v-model="cusId"
              placeholder="请选择客户"
              dict="sys_user,user_company,id"
              condition='{"user_type":"4"}'
              pidField="create_by_id"
              pidValue="ae95dbad4e2e444bb6a1fce9582aac5f"/>
        </a-col>
        </a-row>
        <a-row>
        <a-col :span="24">
          <bar title="报表(当月)" :dataSource="barData"/>
        </a-col>
        </a-row>
      </a-card>
      </a-form>-->
    </a-spin>
  </a-modal>
</template>

<script>

  import { getAction,deleteAction,putAction,postAction} from '@/api/manage'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'
  import Bar from '@/components/chart/Bar'
  export default {
    name: "ReportModal",
    components: {
      JTreeSelect,Bar
    },
    data () {
      return {

        importBatch:'',
        errorMsg:'',
        title:"操作",
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
        barData:[],
        cusId:'de65988d230788f4610ab10f43317422',
        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
        },
        url: {
          getBarDataUrl: "/electronregular/electronChannelOrderRegular/barDatalist",
        },
      }
    },
    created () {
    },
    methods: {
      see () {
        this.visible = true;
        this.initBarData("de65988d230788f4610ab10f43317422")
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
      handleChange(value) {
        console.log(`selected ${value}`);
        this.initBarData(value);
      },
      initBarData(dataString){
        this.barData=[];
        let params={dataString:dataString};
        getAction(this.url.getBarDataUrl,params).then((res)=>{
          if(res.success){
            console.log(res.result)
            for (let i = 0; i < res.result.length; i++) {
              this.barData.push({
                x: res.result[i].transfer+'',
                y: res.result[i].value
              })
            }
          }
        })

      }

    }
  }
</script>

<style lang="less" scoped>

</style>