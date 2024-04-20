<script setup lang="ts">
import { ref } from "vue";
import axios from "axios";

interface Image {
  url: string;
  id: string;
  file: File;
  status: number;
  sizeErr: boolean;
  uploadStatus: boolean;
}

const images = ref<Image[]>([]);
// const uploadStatus = ref<boolean | null>(null);

function multiChange(e: any) {
  const files = e.target.files;
  if (!files) return;
  for (let i = 0; i < files.length; i++) {
    let viewImgUrl = '';
    if(files[i].type.includes('jpeg') || files[i].type.includes('png')){
      viewImgUrl = URL.createObjectURL(files[i]);
    } else if(files[i].type.includes('pdf')){
      viewImgUrl = 'https://play-lh.googleusercontent.com/9XKD5S7rwQ6FiPXSyp9SzLXfIue88ntf9sJ9K250IuHTL7pmn2-ZB0sngAX4A2Bw4w';
    } else if(files[i].type.includes('zip')){
      viewImgUrl = 'https://d1nhio0ox7pgb.cloudfront.net/_img/g_collection_png/standard/512x512/folder_zip.png';
    } else if(files[i].type.includes('sql')){
      viewImgUrl = 'https://www.shareicon.net/data/2015/09/07/97430_document_512x512.png';
    } else if(files[i].type.includes('html')){
      viewImgUrl = 'https://cdn4.iconfinder.com/data/icons/smashicons-file-types-flat/56/22_-_HTML_File_Flat-512.png';
    } else {
      viewImgUrl = 'https://www.iconpacks.net/icons/2/free-file-icon-1453-thumb.png';
    }

    images.value.push({
      id: `${i}-${new Date().getTime()}`,
      url: viewImgUrl,
      file: files[i],
      status: 0,
      sizeErr: false,
      uploadStatus: false
    });
  }
}

const uploadFiles = async () => {
  if (!images.value.length) return;
  for (let i = 0; i < images.value.length; i++) {
    if(images.value[i].status !== 1) {
      if (images.value[i].file.size < 25600000) {
        const response = await uploadSingle(images.value[i].file);
        console.log("response: ", response);
        if(response.status== 200){
          images.value[i].status = 1;
          images.value[i].uploadStatus = true;
        }
      } else {
        images.value[i].uploadStatus = false;
        images.value[i].sizeErr = true;
        images.value[i].status = 2;
        console.log('Size must be less than 25mb!!!');
      }
    }
  }
}

async function uploadSingle(file: File) {
  const data = new FormData();
  data.append('tenantId', 'test');
  data.append('module', 'test');
  data.append('fileName', 'test');
  data.append('file', file);

  return await axios.post('http://192.168.100.241:9999/api/file/upload/public', data);
}

const handleDelete = (id: string) => {
  images.value = images.value.filter(image => image.id !== id);
}
</script>

<template>
  <div>
    <p>Multi Upload</p>
    <div class="images flex flex-wrap w-full h-auto gap-3">
      <div v-for="image in images" :key="image.id">
        <div class="relative">
          <img class="absolute w-[30px] top-0 right-0" v-if="image.uploadStatus" src=https://upload.wikimedia.org/wikipedia/commons/thumb/3/3b/Eo_circle_green_checkmark.svg/768px-Eo_circle_green_checkmark.svg.png?20200417132424>
          <img src="../assets/icons/process-error.svg" class="absolute w-[30px] top-0 right-0" v-if="image.sizeErr">
          <img :src="image.url" alt="image" class="w-[200px] h-[200px]"/>
          <button class="w-[70px] h-[30px] border-1 border-black bg-red-600 rounded mt-2 mb-2" @click="handleDelete(image.id)">Delete</button>
        </div>
      </div>
    </div>
    <label class="buttons border border-black border-dashed flex items-center justify-center flex-col w-[200px] h-[200px]">
      <img class="w-4" src="../assets/icons/upload-file-svgrepo-com.svg">
      <p class="text-neutral-700">Click to upload</p>
      <p class="text-[12px]">SVG,PNG,ZIP</p>
      <div class="uploader absolute opacity-0">
        <input type="file" multiple @change="multiChange">
      </div>
    </label>
    <button @click="uploadFiles" class="w-[100px] h-[30px] border-1 border-black bg-blue-500 rounded mt-2 mb-2 text-white">Upload All</button>
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
