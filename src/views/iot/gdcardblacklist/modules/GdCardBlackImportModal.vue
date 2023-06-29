<template>
  <a-modal
    :title="title"
    :width="width"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item :labelCol="labelCol"
                     :wrapperCol="wrapperCol"
                     label="下载模板">
          <a-button type="primary" @click="exportCardXlsCard('黑名单导入模版')">下载模板</a-button>
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

  import {downFile, httpAction, postAction} from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import JDate from '@/components/jeecg/JDate'

  export default {
    name: "GdCardBlackListModal",
    components: {
      JDate,
      JSearchSelectTag
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:600,
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
        validatorRules: {
          agentId: {rules: [
          ]},
          cusName: {rules: [
          ]},
          cusPhone: {rules: [
          ]},
          cusIdno: {rules: [
          ]},
          payerClientIp: {rules: [
          ]},
          province: {rules: [
          ]},
          city: {rules: [
          ]},
          district: {rules: [
          ]},
          detailAddr: {rules: [
          ]},
          filterFlag: {rules: [
          ]},
          filterMsg: {rules: [
          ]},
          createBy: {rules: [
          ]},
          createTime: {rules: [
          ]},
          updateBy: {rules: [
          ]},
          updateTime: {rules: [
          ]},
        },
        url: {
          add: "/gdcardblacklist/gdCardBlackList/add",
          edit: "/gdcardblacklist/gdCardBlackList/edit",
          importExcelUrl: "/gdcardblacklist/gdCardBlackList/importExcel",
          exportXlsUrl: "/gdcardblacklist/gdCardBlackList/exportXls",
        }
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

        })
      },
      close () {
        this.fileList = [];
        this.$emit('close');
        this.visible = false;
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
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err){
            const { fileList } = this;
            const formData = new FormData();
            fileList.forEach((file) => {
              formData.append('files[]', file);
            });
            console.log("表单提交数据",formData)
            postAction(this.url.importExcelUrl,formData).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
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
      popupCallback(row){

      },


    }
  }
</script>