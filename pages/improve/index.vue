<script setup>
import { ref } from 'vue';
const image = ref(false);
const upscale = ref(2);
const fidelity = ref(0.5);
const url = ref(false);
const loading = ref(false);
const output = ref(false);
const isDisabled = ref(false);

const fileHandle = e => {
  const fileReader = new FileReader()

  fileReader.addEventListener('load', function () {
    image.value = this.result
  })

  fileReader.readAsDataURL(e.target.files[0]);
}

const clickHandle = () => {
  loading.value = true;
  isDisabled.value = true;
  const formData = new FormData()
  formData.append('image', image.value)
  formData.append('fidelity', fidelity.value)
  formData.append('upscale', upscale.value)
  fetch('http://localhost/AI_API/ai_photo_enhancement/backend/index.php?action=start', {
    method: 'post',
    body: formData
  })
    .then(res => res.json())
    .then(res => url.value = res.url)
}

watch(url, (newUrl) => {
  if (newUrl) {
    const interval = setInterval(async function () {
      const formData = new FormData()
      formData.append('url', url.value)
      await fetch('http://localhost/AI_API/ai_photo_enhancement/backend/index.php?action=result', {
        method: 'post',
        body: formData
      })
        .then(res => res.json())
        .then(res => {
          if (res.status === 'processing') {
            loading.value = true;
          }
          if (res.status === 'succeeded') {
            isDisabled.value = false;
            loading.value = false;
            output.value = res.output
            clearInterval(interval);
          }
          if (res.status === 'failed') {
            alert("Failed")
            clearInterval(interval);
            loading.value = false;
          }
        })
    }, 1000)
    return () => {
      clearInterval(interval);
    }
  }
})
</script>

<template>
  <div class="spinner-border text-primary position-absolute" v-if="loading" style="height:500px">
    <span class="sr-only"></span>
  </div>
  <div class="container-fluid" :style="{ opacity: loading ? .3 : .9 }">
    <div class="row justify-content-center align-items-center rounded">
      <div class="col-md-6">
        <p class="fw-bolder font-monospace text-white text-center fs-3"> Artify AI</p>

        <div class="card bg-body shadow-lg">
          <div class="card-header">
            <Navbar></Navbar>
          </div>
          <div class="card-header">
            <div class="row text-center">
              <input class="form-control" type="file" @change="fileHandle" />
              <div class="col-md-6 mt-2">
                <label for="fidelity" class="mr-4 d-block">Benzerlik Oranı </label>
                <input type="range" id="fidelity" v-model="fidelity" min="0" max="1" step="0.01" />
                <span class="h5 ms-3">{{ fidelity }}</span>
              </div>
              <div class="col-md-6 mt-2">
                <label for="upscale" class="mr-4 d-block">Çözünürlük Yükseltme </label>
                <input type="range" v-model="upscale" id="upscale" min="2" max="5" step="1" />
                <span class="h5 ms-3">{{ upscale }}</span>
              </div>
            </div>
          </div>
          <div class="card-body">
            <div class="row text-center">
              <div class="col-md-6 border-end border-primary">
                <div v-if="image">
                  <img :src="image" alt="old_photo" class="rounded w-75" />
                </div>
              </div>
              <div class="col-md-6">
                <div v-if="output">
                  <img :src="output" alt="new_photo" class="rounded w-75" />
                </div>
              </div>
            </div>
          </div>
          <button class="btn btn-primary" :disabled="!image || !fidelity || loading || isDisabled"
            @click="clickHandle">Dönüştür</button>
        </div>
      </div>
    </div>
  </div>
</template>