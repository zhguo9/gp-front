<template>
  <div>
    <h2 class="title">准确率</h2>
    <div class="backend-number-box">
      <span>{{ backendNumber }}</span>
    </div>

    <h2 class="title">选择文件：</h2>
    <ul class="file-list">
      <li v-for="file in files" :key="file.id" class="file-item" @click="processFile(file)">{{ file.name }}</li>
    </ul>

  </div>
</template>

<script>
export default {
  data() {
    return {
      files: [],
      backendNumber: ''
    };
  },
  mounted() {
    this.getFileList(); // 在组件挂载后立即获取文件列表数据
  },
  methods: {
    handleSuccess(response, file, fileList) {
      this.$message({
        message: '文件上传成功',
        type: 'success'
      });
      // 上传成功后重新获取文件列表
      this.getFileList();
    },
    beforeUpload(file) {
      // 在这里可以进行文件类型和大小的校验
      return true;
    },
    async getFileList() {
      try {
        const response = await fetch('http://localhost:8080/files'); // 请替换为你的后端接口地址
        const data = await response.json();
        this.files = data.map((file, index) => ({ id: index, name: file }));
      } catch (error) {
        console.error('Error fetching file list:', error);
      }
    },
    async processFile(file) {
      try {
        this.$message({
          message: '正在进行分词，请稍后',
          type: 'info'
        });
        this.$set(this, 'backendNumber', 0);
        const response = await fetch(`http://localhost:8080/api/process?fileName=${encodeURIComponent(file.name)}`, {
          method: 'POST',
          // 可以根据后端要求设置请求头和其他参数
        });
        const result = await response.text();
        console.log(result);
        const lines = result.split('\n');
        console.log(lines)
        const lastLine = lines[lines.length - 2];
        console.log(lastLine)
        this.$set(this, 'backendNumber', lastLine);
      } catch (error) {
        console.error('Error processing file:', error);
      }
    }
  }
}
</script>

<style scoped>
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

.title {
  text-align: center;
}

.file-list {
  list-style: none;
  padding: 0;
  text-align: center;
}

.file-item {
  background-color: #f0f0f0;
  padding: 10px;
  margin-bottom: 5px;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  cursor: pointer; /* 添加鼠标指针样式，表明可以点击 */
}

.backend-number-box {
  margin-top: 20px;
  text-align: center;
  display: flex;
  justify-content: center;
}

.backend-number-box span {
  padding: 10px;
  background-color: #e0e0e0;
  border-radius: 5px;
}
</style>
