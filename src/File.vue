<template>
  <div>
    <h3>File Upload (without yup)</h3>
    
    <input 
      type="file" 
      @change="handleFileChange"
    />
    <div style="color: red">{{ errors.file }}</div>

  </div>
</template>

<script lang="ts" setup>
import { useForm } from 'vee-validate'

const validationSchema = {
  file: (value: FileList | null) => {
    if (!value || value.length === 0) {
      return 'Fichier requis'
    }
    
    const file = value[0]
    if (file.type !== 'application/pdf') {
      return 'Le fichier doit être au format PDF'
    }
    
    return true
  }
}

const { errors, defineField, setFieldValue } = useForm({
  validationSchema,
  validateOnMount: true
})

const [file] = defineField('file')

const handleFileChange = (event: Event) => {
  const target = event.target as HTMLInputElement
  setFieldValue('file', target.files)
}
</script>
