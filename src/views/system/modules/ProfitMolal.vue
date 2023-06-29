<template>
  <a-modal
    title="设置用户佣金"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleSubmit"
    @cancel="close"
    cancelText="关闭"
    style="top:20px;"
  >
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="佣金分配方式" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag v-model="validatorRules.profitType"  placeholder="请选择使用状态" dict-code="profit_type" ></j-dict-select-tag>
        </a-form-item>
        <a-form-item v-if="validatorRules.profitType == 2" label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-textarea v-model="validatorRules.remark" placeholder="请输入备注名称"></a-textarea>
        </a-form-item>
        <a-form-item v-if="validatorRules.profitType == 1">
          <count-molal
            ref="childrenDom"
            :title="`${PARTONE}`"
            :wrapHeight="360"
            :arr="arr"
          />
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
    import CountMolal from './CountMolal';
    import { httpAction,getAction } from '@/api/manage'
    const PARTONE = 'partOne'
    export default {
        name: "ProfitMolal",
        components: {
            CountMolal
        },
        data() {
            return {
                agentId: "",
                dictOperatorOptions:[],
                profitShow: false,
                arr: [],
                PARTONE,
                param: {
                    field: '' // 模拟接口接收的参数
                },
                form: this.$form.createForm(this),
                visible: false,
                confirmLoading: false,
                confirmDirty: false,
                labelCol: {
                    xs: { span: 24 },
                    sm: { span: 5 },
                },
                wrapperCol: {
                    xs: { span: 24 },
                    sm: { span: 16 },
                },
                validatorRules: {
                    profitType: "",
                    remark: "",
                    userId: "",
                    profitData: "",
                    id:"",
                    agentId:""
                },
                url: {
                    add: "/sys/telecomAgent/addProfit",
                    getByUserId: "/sys/telecomAgent/getByUserId",
                    initOperatorUrl: "/electronchannelagent/electronChannelAgent/getAgentByCusId",
                },
                success : true
            }
        },
        methods: {
            getAgentByCusId(cusId){
                let params = {cusId:cusId}
                getAction(this.url.initOperatorUrl,params).then((res)=>{
                    if(res.success){
                        this.dictOperatorOptions = res.result;
                    }
                })


            },
            show (record) {
                // console.log("子组件agentId:" + this.agentId)
                // console.log(record)
                this.getAgentByCusId(record.id);
                // console.log(this.dictOperatorOptions)
                this.validatorRules.agentId = record.agentId
                this.visible = true;
                this.validatorRules.userId = record.id
                const partOneArr = [];
                //根据userId查询佣金配置并回显
                getAction(this.url.getByUserId,{id:record.id,agentId:record.agentId}).then((res => {
                    // this.validatorRules.agentId = res.result[0].agentId
                    for (let i = 0; i < res.result.length; i++) {
                       const obj = {
                           countBegin: res.result[i].countBegin,
                           countEnd: res.result[i].countEnd,
                           profit: res.result[i].profit,
                       }
                       if (obj.countBegin !== null && obj.countEnd !== null && obj.profit !== null) {
                           partOneArr.push(obj)
                       }
                   }
                    this.arr = partOneArr;
                    //回显
                    if(this.arr.length > 0) {
                        this.validatorRules.profitType = "1"
                    } else if (res.result[0].profitType = 2) {
                        this.validatorRules.profitType = "2"
                        this.validatorRules.remark = res.result[0].remark
                    }
                }))

            },
            close () {
                this.$emit('close');
                this.visible = false;
                this.disableSubmit = false;
                this.arr = [];
                this.profitShow = false;
                this.validatorRules.profitType = null;
                this.validatorRules.remark = "";
                this.validatorRules.userId = "";
                this.validatorRules.profitData = "";
                this.validatorRules.id = "";
                this.validatorRules.agentId = "";
            },
            handleSubmit () {
                const { form: { validateFields } } = this
                validateFields((errors, values) => {
                    if (!errors && this.validatorRules.profitType != null) {
                        const partOneArr = [];
                        if(values[`${PARTONE}countBegin`] !== undefined) {
                            (values[`${PARTONE}countBegin`]).forEach((item, index) => {
                                const obj = {
                                    countBegin: item,
                                    countEnd: values[`${PARTONE}countEnd`][index],
                                    profit: values[`${PARTONE}profit`][index],
                                }
                                partOneArr.push(obj)
                            })
                        }
                        this.validatorRules.profitData = partOneArr;


                        for(let i = 0; i < this.validatorRules.profitData.length; i++) {
                            if (i == 0) {
                                if (this.validatorRules.profitData[i].countBegin >= this.validatorRules.profitData[i].countEnd ) {
                                    this.success = false;
                                } else {
                                    this.success = true;
                                }
                            }else if (i >= 1){
                                if (!this.validatorRules.profitData[i].countBegin >= this.validatorRules.profitData[i-1].countEnd + 1 ) {
                                    this.success = false
                                } else if (this.validatorRules.profitData[i].countBegin >= this.validatorRules.profitData[i].countEnd) {
                                    this.success = false
                                } else {
                                    this.success = true
                                }
                            }
                        }
                    }
                })
                let httpurl = this.url.add;
                let method = 'post';
                let formData = this.validatorRules


                if (this.success) {
                    httpAction(httpurl, formData, method).then((res) => {
                        if (this.validatorRules.profitType === "" ) {
                            this.$message.warning("请选择佣金分配方式");
                        } else if (res.success) {
                            this.$message.success(res.message);
                            this.$emit('ok');
                            this.close();
                        } else {
                            this.$message.warning("暂无设置权限，请重新操作");
                        }
                    })
                } else {
                    this.$message.warning("数据有误,请核对");
                }
            }
        },
    }
</script>

<style scoped>

</style>