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

    <h2 class="title">文件列表</h2>
    <ul class="file-list">
      <li v-for="fileName in fileNames" :key="fileName" class="file-item">{{ fileName }}</li>
    </ul>

  </div>
</template>

<script>
export default {
  data() {
    return {
      fileNames: []
    };
  },
  mounted() {
    this.getFileList();
  },
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
    },
    async getFileList() {
      try {
        const response = await fetch('http://localhost:8080/files'); // 请替换为你的后端接口地址
        const data = await response.json();
        this.fileNames = data;
        console.error('Error fetching file list:');
      } catch (error) {
        console.error('Error fetching file list:', error);
      }
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
.file-list {
  list-style: none;
  padding: 0;
}

.file-item {
  background-color: #f0f0f0;
  padding: 10px;
  margin-bottom: 5px;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.title {
  text-align: center;
}
</style>
