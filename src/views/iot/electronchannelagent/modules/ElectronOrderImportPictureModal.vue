<template>
  <a-modal
    title="上传图片"
    :width="500"
    :visible="visible"
    :confirmLoading="uploading"
    @cancel="handleClose">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item :labelCol="labelCol"
                     :wrapperCol="wrapperCol"
                     label="选择文件">
          <a-upload
            name="file"
            :multiple="true"
            accept=".jpg,.png,.jpeg"
            :fileList="fileList"
            :remove="handleRemove"
            :beforeUpload="beforeUpload">
            <a-button>
              <a-icon type="upload" />
              点击上传
            </a-button>
          </a-upload>
        </a-form-item>
      </a-form>
    </a-spin>

    <template slot="footer">
      <a-button @click="handleClose">关闭</a-button>
      <a-button
        type="primary"
        @click="handleImport"
        :disabled="fileList.length === 0"
        :loading="uploading">
        {{ uploading ? '上传中...' : '开始上传' }}
      </a-button>
    </template>

  </a-modal>
</template>

<script>
  import { postAction, downFile } from '@/api/manage'

  export default {
    name: 'ElectronOrderImportPictureModal',
    props:{
      url:{
        type: String,
        default: '',
        required: false
      }
    },
    data(){
      return {
        visible:false,
        uploading:false,
        fileList:[],
        agentId: "",
        uploadAction:'',
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        form: this.$form.createForm(this)
      }
    },
    watch: {
      url (val) {
        if(val){
          this.uploadAction = window._CONFIG['domianURL']+val
        }
      }
    },
    created () {
      this.uploadAction = window._CONFIG['domianURL']+this.url
    },

    methods:{
      handleClose(){
        this.visible=false
      },
      show(){
        this.fileList = [];
        this.uploading = false;
        this.visible = true
      },
      handleRemove(file) {
        const index = this.fileList.indexOf(file);
        const newFileList = this.fileList.slice();
        newFileList.splice(index, 1);
        this.fileList = newFileList
      },
      beforeUpload(file) {
        this.fileList = [...this.fileList, file];
        return false;
      },
      handleImport() {
        const { fileList } = this;
        const formData = new FormData();
        fileList.forEach((file) => {
          formData.append('files[]', file);
          debugger
          formData.append('agentId',this.agentId);
        });
        console.log(formData.get("agentId"))
        this.uploading = true;
        postAction("/electronchannelagent/electronChannelAgent/uploadPicture", formData,this.agentId).then((res) => {
          this.uploading = false;
          if(res.success){
            this.$message.success(res.message);
            this.visible=false;
            this.$emit('ok')
          }else{
            this.$message.warning(res.message)
          }
        })
      },
    }
  }
</script>

<style scoped>

</style>