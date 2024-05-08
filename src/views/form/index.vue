<template>
  <div>
    <div class="container">
    <!-- 左侧功能 -->
    <div class="left">
      <h2 class="title">选择FNA文件</h2>
      <ul class="file-list">
        <li v-for="file in files" :key="file.id" class="file-item" @click="processFile(file)">{{ file.name }}</li>
      </ul>
    </div>

    <!-- 右侧图片 -->
    <div class="right">
      <img src="../../assets/transfer.png" alt="图片" style="width: 600px; height: auto;">
    </div>
  </div>

    <h2 class="title">分词结果</h2>
    <!-- 新增的输出框 -->
    <div class="container">
      <div class="textarea-container">
        <div class="title-container">
          <h3>模型分词结果</h3>
          <div class="spacer"></div> <!-- 空白的 div 元素 -->
          <button @click="saveToExcel">保存到本地文件</button> <!-- 新增的按钮 -->
        </div>
        <textarea v-model="output1" rows="40" cols="80" readonly></textarea>
      </div>
      <div class="textarea-container">
        <div class="title-container">
          <h3>原有位点</h3>
        </div>
        <textarea v-model="output2" rows="40" cols="80" readonly></textarea>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      files: [],
      backendNumber: '',
      output1: '',
      output2: '',
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
        //进行分词
        // this.$set(this, 'backendNumber', 0);
        const response = await fetch(`http://localhost:8080/api/process?fileName=${encodeURIComponent(file.name)}`, {
          method: 'POST',
          // 可以根据后端要求设置请求头和其他参数
        });
        const result = await response.text();
        console.log(result);
        const lines = result.split('\n');
        // console.log(lines)
        // const lastLine = lines[lines.length - 2];
        // 排除第一行
        const remainingLines = lines.slice(1);
        // 将剩余行连接起来
        const output = remainingLines.join('\n');
        this.$set(this, 'output1', output);

        //得到原位点
        const response1 = await fetch(`http://localhost:8080/api/getoriginal?fileName=${encodeURIComponent(file.name)}`, {
          method: 'POST',
          // 可以根据后端要求设置请求头和其他参数
        });
        const result1 = await response1.text();
        const lines1 = result1.split('\n');
        // 排除第一行
        // const remainingLines1 = lines1.slice(1);
        // 将剩余行连接起来
        const outputt = lines1.join('\n');
        this.$set(this, 'output2', outputt);
        
        this.$message({
          message: '分词完成！',
          type: 'success'
        });
      } catch (error) {
        console.error('Error processing file:', error);
      }
    },
    saveToExcel() {
      const XLSX = require('xlsx');
      const wb = XLSX.utils.book_new();
      
      // 将 output1 按行分割成二维数组，并去除每个词两侧的空格
      const rows = this.output1.split('\n').map(row => row.split(/\s*\|\s*/).map(word => word.trim()));
      
      // 将二维数组转换为工作表
      const ws = XLSX.utils.aoa_to_sheet(rows);
      
      // 将工作表添加到工作簿
      XLSX.utils.book_append_sheet(wb, ws, 'Sheet1');
      
      // 写入到文件
      XLSX.writeFile(wb, 'output.xlsx');
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
.left {
  flex: 1; /* 左侧占据剩余空间 */
  margin-right: 20px; /* 右侧留出 20 像素的间距 */
}
.right {
  flex: 1; /* 右侧占据剩余空间 */
}
.file-list {
  list-style: none;
  /* padding: 0; */
  text-align: center;
}

.file-item {
  background-color: #f0f0f0;
  display: inline-block;
  padding: 10px;
  margin-bottom: 5px;
  margin-right: 20px; /* 添加水平间距 */
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  cursor: pointer; /* 添加鼠标指针样式，表明可以点击 */
  /* font-size: 50; */
}

.title-container {
  display: flex;
  justify-content: center;
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

.spacer {
  flex: 0.2; /* 占据剩余的空间 */
}

.container {
  display: flex;
  width: 100%;
}

.textarea-container {
  flex: 1;
  margin: 10px;
  width: 50%;
}
</style>
