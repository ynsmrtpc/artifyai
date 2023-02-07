<script setup>
import { ref } from 'vue';

const prompt = ref(null);
const negative_prompt = ref(null);
const strength = ref(0.8);
const width = ref("768");
const height = ref("768");
const num_outputs = ref(1);
const url = ref(false);
const loading = ref(false);
const output = ref(false);
const isDisabled = ref(false);
const IsShow = ref(false);

const clickHandle = () => {
    isDisabled.value = true;
    loading.value = true;
    const formData = new FormData();
    formData.append("prompt", prompt.value);
    formData.append("negative_prompt", negative_prompt.value);
    formData.append("strength", strength.value);
    formData.append("width", width.value);
    formData.append("height", height.value);
    formData.append("num_outputs", num_outputs.value);

    fetch(
        "https://upscale-image-php.000webhostapp.com/texttoimage.php?action=start",
        {
            method: "post",
            body: formData,
        }
    )
        .then((res) => res.json())
        .then((res) => console.log(res.url));
};

watch(url, (newUrl) => {
    if (newUrl) {
        const interval = setInterval(async function () {
            const formData = new FormData()
            formData.append('url', url.value)
            await fetch('https://upscale-image-php.000webhostapp.com/texttoimage.php?action=result', {
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
    <div class="spinner-border text-primary position-absolute" v-if="loading">
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

                    <div class="card-header d-flex justify-content-between">
                        <input className="form-control border-success output-0 shadow-none me-3" type="text"
                            v-model="prompt" placeholder="Prompt giriniz..." />
                        <button type="button"
                            className="btn btn-success rounded-circle btn-sm border-2 output-0  h-100 w-0"
                            @click="IsShow = !IsShow" data-bs-toggle=" tooltip" data-bs-placement="top"
                            title="Ekstra Özellikleri Gör">
                            &#43;
                        </button>
                    </div>
                    <div class="card-body">
                        <div v-if="IsShow">
                            <div class="row border-top mt-3 pt-2 border-success">
                                <div class="col-md-6">
                                    <input class="form-control border-2 output-0 shadow-none mt-1" type="text"
                                        v-model="negative_prompt" placeholder="Negative Prompt giriniz..." />
                                </div>
                                <div class="col-md-6 d-flex justify-content-start align-items-center">
                                    <label for="num_outputs">Adet </label>
                                    <input class="bg-warning border-2 output-0 shadow-none mt-1 w-75 mx-4"
                                        id="num_outputs" v-model.number="num_outputs" min="1" max="4" step="1"
                                        type="range" />
                                    <span> {{ num_outputs }}</span>
                                </div>
                                <div class="row">
                                    <div class="col-md-4">
                                        <label for="width">Genişlik Seçin</label>
                                        <select class="form-control border-2 output-0 shadow-none mt-1" type="text"
                                            v-model="width">
                                            <option value="128">128</option>
                                            <option value="256">256</option>
                                            <option value="384">384</option>
                                            <option value="448">448</option>
                                            <option value="512">512</option>
                                            <option value="576">576</option>
                                            <option value="640">640</option>
                                            <option value="704">704</option>
                                            <option value="768">768</option>
                                            <option value="832">832</option>
                                            <option value="896">896</option>
                                            <option value="960">960</option>
                                            <option value="1024">1024</option>
                                        </select>
                                    </div>
                                    <div class="col-md-4">
                                        <label for="height">Yükseklik Seçin</label>
                                        <select class="form-control border-2 output-0 shadow-none mt-1" type="text"
                                            id="height" v-model="height">
                                            <option value="128">128</option>
                                            <option value="256">256</option>
                                            <option value="384">384</option>
                                            <option value="448">448</option>
                                            <option value="512">512</option>
                                            <option value="576">576</option>
                                            <option value="640">640</option>
                                            <option value="704">704</option>
                                            <option value="768">768</option>
                                            <option value="832">832</option>
                                            <option value="896">896</option>
                                            <option value="960">960</option>
                                            <option value="1024">1024</option>
                                        </select>
                                    </div>
                                    <div className="col-md-4">
                                        <label htmlFor="strength">Prompt Strength Giriniz</label>
                                        <input className="form-control border-2 output-0 shadow-none mt-1" type="number"
                                            id="strength" v-model="strength" />
                                    </div>

                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-12 mb-0">
                        <div v-if="output">
                            <img :src="output" alt="output_photo" class="rounded w-75" />
                        </div>
                    </div>
                    <button class="btn btn-success w-100" :disabled="!prompt || loading || isDisabled"
                        @click="clickHandle">
                        Oluştur
                    </button>

                </div>
            </div>
        </div>
    </div>
</template>