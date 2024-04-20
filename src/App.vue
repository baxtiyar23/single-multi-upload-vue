<script setup lang="ts">
import {ref} from 'vue';
import axios from "axios";
import SingleFileUpload from "@/components/singleFileUpload.vue";
import MultiFileUpload from "@/components/multiFileUpload.vue";


</script>

<template>
<div class="flex flex-col items-start justify-center m-7">
  <SingleFileUpload/>
  <MultiFileUpload/>
</div>
</template>

<style scoped>

</style>