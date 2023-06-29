<template>
  <a-modal
    :title="title"
    :width="600"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="运营商">
          <j-dict-select-tag v-decorator="[ 'operatorId', validatorRules.operatorId]" :triggerChange="true" placeholder="请选择运营商" style="width: 80%"
                dictCode="electron_operator_config,operator,id"/>
        </a-form-item>

        <a-form-item :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="下载模板">
          <a-button type="primary" @click="exportCardXlsCard('佣金导入模版')">下载模板</a-button>
        </a-form-item>

        <a-form-item :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="选择文件">
          <a-upload
            name="file"
            :multiple="true"
            accept=".xls,.xlsx"
            :fileList="fileList"
            :remove="handleRemove"
            :beforeUpload="beforeUpload">

            <a-button>
              <a-icon type="upload"/>
              选择导入文件
            </a-button>
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
          </a-upload>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction,downFile } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import {queryOperator,cardImportExcel,queryLowerAgent } from '@/api/api'
  import { postAction } from '@/api/manage'

  export default {
    name: "ElectronOperationIncomeImport",
    data () {
      return {
        title:"操作",
        visible: false,
        model: {},
        fileList:[],

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
          operatorId:{
            rules: [{
              required: true, message: '请选择运营商!'
            }]
          }

        },
        url: {
          exportXlsUrl: "/electronoperationincome/electronOperationIncome/exportXls",
          importExcelUrl: "electronoperationincome/electronOperationIncome/importExcel"
        },
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
		  //时间格式化

        });

      },
      close () {
        this.fileList = [];
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if(!err){
            that.confirmLoading = true;
            const { fileList } = this;
            const formData = new FormData();
            formData.append('operatorId',values.operatorId);
            if(fileList.length==0){
              that.$message.warning("请上传文件");
              that.confirmLoading = false;
              return;
            }
            fileList.forEach((file) => {
              formData.append('files[]', file);
            });
            postAction(that.url.importExcelUrl, formData).then((res) => {
              if(res.success){
                that.$message.success(res.message)
                that.visible=false
                that.$emit('ok')
              }else{
                that.$message.warning(res.message)
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }
         })
      },
      handleCancel () {
        this.close()
      },
      beforeUpload(file) {
        this.fileList = [...this.fileList, file]
        return false;
      },
      handleRemove(file) {
        const index = this.fileList.indexOf(file);
        const newFileList = this.fileList.slice();
        newFileList.splice(index, 1);
        this.fileList = newFileList
      },
      exportCardXlsCard(fileName){
        let param = null;

        downFile(this.url.exportXlsUrl,param).then((data)=>{
          if (!data) {
          this.$message.warning("文件下载失败")
          return
        }
        if (typeof window.navigator.msSaveBlob !== 'undefined') {
          window.navigator.msSaveBlob(new Blob([data]), fileName+'.xls')
        }else{
          let url = window.URL.createObjectURL(new Blob([data]))
          let link = document.createElement('a')
          link.style.display = 'none'
          link.href = url
          link.setAttribute('download', fileName+'.xls')
          document.body.appendChild(link)
          link.click()
          document.body.removeChild(link); //下载完成移除元素
          window.URL.revokeObjectURL(url); //释放掉blob对象
        }
      })

      }


    }
  }
</script>
