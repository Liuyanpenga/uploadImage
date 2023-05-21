<template>
  <div>
    <a-upload directory :showUploadList="false" action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
      @change="handleFolderUpload" :before-upload="beforeUpload">
      <a-button>
        <upload-outlined></upload-outlined>
        上传文件夹
      </a-button>
    </a-upload>
    <div v-if="imageFiles" class="clearfix">
      <a-upload class="upload-list-inline" v-model:file-list="imageFiles"
        action="https://www.mocky.io/v2/5cc8019d300000980a055e76" list-type="picture" @preview="handlePreview">
      </a-upload>
      <a-modal :visible="previewVisible" :title="previewTitle" :footer="null" @cancel="handleCancel">
        <img alt="图片" style="width: 100%" :src="previewImage" />
      </a-modal>
    </div>
    <!-- <a-button type="primary" @click="upload">上传</a-button> -->
  </div>
</template>

<script setup>
import { ref } from "vue"
import { Upload } from 'ant-design-vue';
import { UploadOutlined } from '@ant-design/icons-vue';

const previewVisible = ref(false); // 图片展示框
const previewImage = ref(''); // 图片展示地址
const previewTitle = ref(''); // 展示框标题
const imageFiles = ref() // 图片数据列表

// 文件上传改变事件(取文件)
const handleFolderUpload = (e) => {
  const fileList = e.fileList
  const filesArray = Array.from(fileList)
  imageFiles.value = filesArray.filter(file => file.type.includes('image'));
  // console.log(imageFiles.value, '---------')
}

// 上传前事件(筛选上传图片)
const beforeUpload = (file) => {
  const isImage = file.type.includes('image');

  return isImage || Upload.LIST_IGNORE;
}

// 图片转换
const getBase64 = (file) => {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => resolve(reader.result);
    reader.onerror = error => reject(error);
  });
}

// 预览图标功能展示
const handlePreview = async file => {
  if (!file.url && !file.preview) {
    file.preview = await getBase64(file.originFileObj);
  }
  previewImage.value = file.url || file.preview;
  previewVisible.value = true;
  previewTitle.value = file.name || file.url.substring(file.url.lastIndexOf('/') + 1);
};

// 取消展示
const handleCancel = () => {
  previewVisible.value = false;
  previewTitle.value = '';
};


const upload = () => {
  // const imageFiles = files.value.filter(file => file.type.includes('image'));
}

</script>
<style scoped>
.clearfix {
  margin-top: 24px;
}

.upload-list-inline :deep(.ant-upload-list-item) {
  float: left;
  width: 200px;
  margin-right: 8px;
}

.upload-list-inline [class*='-upload-list-rtl'] :deep(.ant-upload-list-item) {
  float: right;
}
</style>