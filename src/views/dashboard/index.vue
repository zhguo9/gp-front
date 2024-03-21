<template>
  <div>
    <el-upload
      class="upload-demo"
      action="http://localhost:8080/api/upload"
      :on-success="handleSuccess"
      :before-upload="beforeUpload"
      drag
      multiple>
      <i class="el-icon-upload"></i>
      <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
    </el-upload>
  </div>
</template>

<script>
export default {
  methods: {
    handleSuccess(response, file, fileList) {
      this.$message({
        message: '文件上传成功',
        type: 'success'
      });
    },
    beforeUpload(file) {
      // 在这里可以进行文件类型和大小的校验
      return true;
    }
  }
}
</script>

<style>
.upload-demo {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 200px;
  border: 1px dashed #409EFF;
  border-radius: 6px;
  background-color: #f0f0f0;
  color: #909399;
  cursor: pointer;
}
</style>
