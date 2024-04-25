<script setup lang="ts">
import { ref } from 'vue';
import axios from "axios";

const singleFile = ref<File | null>(null);
const uploadStatus = ref<boolean | null>(null);
const sizeError = ref<boolean | null>(null);
const viewImg = ref<string | null>(null);

const singleUpload = async () => {
  if (singleFile.value) {
    if (singleFile.value.size < 25600000 && uploadStatus.value===false) {
      const response = await uploadSingle(singleFile.value);
      const url = response.data.objectName as string;
      sizeError.value = false;
      uploadStatus.value = true;
      setViewImg(singleFile.value.type, url);
    } else {
      console.error("File must be less than 25mb!!!");
      sizeError.value = true;
      uploadStatus.value = false;
    }
  } else {
    uploadStatus.value = false;
  }
};

async function uploadSingle(file: File) {
  const data = new FormData();
  data.append('tenantId', 'test');
  data.append('module', 'test');
  data.append('fileName', 'test');
  data.append('file', file);

  return await axios.post('http://192.168.100.241:9999/api/file/upload/public', data);
}

function singleChange(e: any) {
  if (e.target.files && e.target.files.length > 0) {
    sizeError.value = false;
    uploadStatus.value = false;
    singleFile.value = e.target.files[0];
    viewImg.value = URL.createObjectURL(e.target.files[0]);
    setViewImg(e.target.files[0].type);
  }
}

function setViewImg(fileType: string, url?: string) {
  if (fileType.includes('jpeg')) {
    viewImg.value = url ? `http://192.168.100.241:9999/api/file/view-image/${url}` : viewImg.value;
  } else if (fileType.includes('png')) {
    viewImg.value = URL.createObjectURL(singleFile.value as File);
  } else if (fileType.includes('pdf')) {
    viewImg.value = 'https://play-lh.googleusercontent.com/9XKD5S7rwQ6FiPXSyp9SzLXfIue88ntf9sJ9K250IuHTL7pmn2-ZB0sngAX4A2Bw4w';
  } else if (fileType.includes('zip')) {
    viewImg.value = 'https://d1nhio0ox7pgb.cloudfront.net/_img/g_collection_png/standard/512x512/folder_zip.png';
  } else if (fileType.includes('sql')) {
    viewImg.value = 'https://www.shareicon.net/data/2015/09/07/97430_document_512x512.png';
  } else if (fileType.includes('html')) {
    viewImg.value = 'https://cdn4.iconfinder.com/data/icons/smashicons-file-types-flat/56/22_-_HTML_File_Flat-512.png';
  } else {
    viewImg.value = 'https://www.iconpacks.net/icons/2/free-file-icon-1453-thumb.png';
  }
}
</script>

<template>
  <div class="grid gap-2 w-[200px] h-[300px] p-2">
    <p>Single Upload</p>
    <label class="buttons border border-black border-dashed flex items-center justify-center flex-col relative" :style="{ backgroundImage: `url(${viewImg})` }">
      <img class=" absolute w-[30px] top-0 right-0" v-if="uploadStatus" src=https://upload.wikimedia.org/wikipedia/commons/thumb/3/3b/Eo_circle_green_checkmark.svg/768px-Eo_circle_green_checkmark.svg.png?20200417132424 alt="">
      <img src="../assets/icons/process-error.svg" class="absolute w-[30px] top-0 right-0" v-if="sizeError" alt="">
      <img v-if="!viewImg" class="w-4" src="../assets/icons/upload-file-svgrepo-com.svg" alt="">
      <p v-if="!viewImg" class="text-neutral-700">Click to upload</p>
      <p v-if="!viewImg" class="text-[12px]">SVG,PNG,ZIP</p>
      <input class="hidden" type="file" @change="singleChange">
    </label>

    <button @click="singleUpload" class="btn btn-blue">Upload</button>
  </div>
</template>

<style scoped>
.btn {
  @apply font-bold py-2 px-4 rounded;
}

.btn-blue {
  @apply bg-blue-500 text-white;
}

.btn-blue:hover {
  @apply bg-blue-700;
}

.buttons {
  width: 200px;
  height: 200px;
  background-position: center;
  background-size: cover;
}
</style>