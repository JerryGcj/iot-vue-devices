<template>
  <a-modal
    title="请选择运营商"
    :width="500"
    :visible="flag"
    @ok="openProfit"
    @cancel="closeAgent"
    cancelText="关闭"
    style="top:20px;"
  >
    <a-spin :spinning="confirmLoading">
      <a-form>
        <a-form-item label="运营商" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select
            allow-clear
            v-model="validatorRules.agentId"
            mode="result"
            style="width: 100%"
            placeholder="请选择"
            :options="dictOperatorOptions"
            :autoClearSearchValue="false"
            :maxTagTextLength=3
            :maxTagCount=3
          ></a-select>
        </a-form-item>
      </a-form>
    </a-spin>
    <profit-molal ref="profitmodal"/>
  </a-modal>
</template>

<script>
    import ProfitMolal from './ProfitMolal';
    import { httpAction,getAction } from '@/api/manage'
    export default {
        name: "AgentMolal",
        components:{
            ProfitMolal
        },
        data() {
            return {
                labelCol: {
                    xs: { span: 24 },
                    sm: { span: 5 },
                },
                wrapperCol: {
                    xs: { span: 24 },
                    sm: { span: 16 },
                },
                confirmLoading: false,
                dictOperatorOptions:[],
                flag: false,
                url: {
                    initOperatorUrl: "/electronchannelagent/electronChannelAgent/getAgentByCusId",
                },
                validatorRules: {
                    agentId:""
                },
                record: null
            }
        },
        methods:{
            show(record) {
                this.record = record
                // this.record.agentId = this.validatorRules.agentId
                this.flag = true
                this.getAgentByCusId(record.id);
            },
            getAgentByCusId(cusId){
                let params = {cusId:cusId}
                getAction(this.url.initOperatorUrl,params).then((res)=>{
                    if(res.success){
                        this.dictOperatorOptions = res.result;
                    }
                })
            },
            openProfit() {
                this.record.agentId = this.validatorRules.agentId
                this.$refs.profitmodal.show(this.record);
                // this.closeAgent();
            },
            closeAgent() {
                this.flag = false
                this.validatorRules.agentId = ""
            }
        }
    }
</script>

<style scoped>

</style>