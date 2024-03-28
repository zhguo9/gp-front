<!-- ParentComponent.vue -->

<template>
    <div>
      <h1>文件管理</h1>
      <FileListDisplay :fileNames="fileNames" />
    </div>
  </template>
  
  <script>
  import FileListDisplay from './index.vue';
  
  export default {
    components: {
      FileListDisplay
    },
    data() {
      return {
        fileNames: []
      };
    },
    mounted() {
      this.getFileList();
    },
    methods: {
      async getFileList() {
        try {
          const response = await fetch('http://localhost:8080/files'); // 请替换为你的后端接口地址
          const data = await response.json();
          this.fileNames = data;
          console.log(data)
        } catch (error) {
          console.error('Error fetching file list:', error);
        }
      }
    }
  };
  </script>
  