<template>
  <div>
    <h3>File Upload Array</h3>

    <div
      style="display: grid; grid-template-columns: 1fr auto"
      v-for="(item, index) in fields"
      :key="index"
    >
      <div>
        <input
          type="file"
          @change="(event) => handleFileChange(event, index)"
        />
        <div style="color: red">
          {{ getFieldError(`files[${index}].file`) }}
        </div>
      </div>

      <div>
        <button @click.prevent="remove(index)" type="button">🗑️</button>
      </div>
    </div>

    <button @click.prevent="push({ file: null })" type="button">
      ➕ Add File
    </button>
  </div>

  <h5>Selected files</h5>
  <ul>
    <li
      v-for="(fileObj, index) in values.files.filter((f) => f.file !== null)"
      :key="index"
    >
      {{ fileObj.file!.name }} {{ fileObj.file!.size }}
    </li>
  </ul>
</template>

<script lang="ts" setup>
import { useForm, useFieldArray } from "vee-validate";
import * as yup from "yup";

const validationSchema = yup.object({
  files: yup.array().of(
    yup.object({
      file: yup
        .mixed<File>()
        .required("Fichier requis")
        .test("is-pdf", "Le fichier doit être au format PDF", (value) => {
          if (!value) return false;
          return value.type === "application/pdf";
        })
        .test(
          "maj",
          "Le nom de fichier doit commencer par une majuscule",
          (value) => {
            if (!value) return false;
            return value.name.charAt(0).toUpperCase() === value.name.charAt(0);
          }
        ),
    })
  ),
});

const { errors, values } = useForm({
  validationSchema,
  initialValues: {
    files: [],
  } as { files: { file: File | null }[] },
});

const { fields, push, remove, update } = useFieldArray("files");

const getFieldError = (fieldPath: string): string | undefined => {
  return errors.value[fieldPath as keyof typeof errors.value];
};

const handleFileChange = (event: Event, index: number) => {
  const target = event.target as HTMLInputElement;
  const file = target.files?.[0] || null;
  update(index, { file });
};
</script>
