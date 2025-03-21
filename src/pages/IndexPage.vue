<template>
  <q-page class="flex flex-center">
    <q-card>
      <q-card-section>
        <h2 class="text-h2 title" color="blue">AI-Agent QR Code Generator</h2>
      </q-card-section>
      <q-separator></q-separator>
      <q-card-section class="row q-gutter-y-md">
        <q-input
          v-model="dataInput"
          label="QR data"
          outlined
          placeholder="Here your data"
          class="col-12"
        ></q-input>

        <q-file
          color="purple-12"
          v-model="imageSelected"
          label="Select image"
          class="col-12"
          accept="image/*"
          outlined
        >
          <template v-slot:prepend>
            <q-icon name="attach_file" />
          </template>
        </q-file>
        <div class="flex justify-end full-width">
          <q-btn @click="generateQR" label="Generate QR Code" color="blue"></q-btn>
        </div>
      </q-card-section>
      <q-separator></q-separator>
      <div v-if="imageUrl" class="flex row flex-center">
        <q-img
          :src="imageUrl"
          spinner-color="primary"
          class="q-mt-md"
          style="max-width: 300px; max-height: 300px"
        />
      </div>

      <q-banner v-else class="bg-grey-3 q-mt-md"> No image loaded yet </q-banner>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref } from 'vue'
import { api } from 'src/boot/axios'
const dataInput = ref('')
const imageSelected = ref(null)
const imageUrl = ref('')

const generateQR = async () => {
  const formData = new FormData()
  formData.append('img', imageSelected.value)
  formData.append('data', dataInput.value.trim())

  try {
    const response = await api.post('qr/create_qr', formData, {
      headers: { 'Content-Type': 'multipart/form-data' },
      responseType: 'blob',
    })
    const imageBlob = new Blob([response.data], { type: response.headers['content-type'] })
    imageUrl.value = URL.createObjectURL(imageBlob)
    console.log('Upload success:', response.data)
  } catch (error) {
    console.error('Upload failed:', error)
  }
}
</script>
<style lang="css">
.title {
  font-size: 1.7rem;
  font-weight: bold;
  text-align: center;
  text-transform: uppercase;
  margin-bottom: 16px;
  color: #2196f3;
}
</style>
