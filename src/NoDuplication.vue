<template>
  <h3>UseFieldArray - Erreur sur plusieurs champs</h3>
  <form>
    <div
      style="display: grid; grid-template-columns: 1fr 1fr 1fr; width: 500px;"
      v-for="(item, index) in fields"
      :key="index"
    >
      <div>
        <input placeholder="Clé" type="text" v-model="item.value.key" />
        <div style="color: red">{{ getFieldError(`rows[${index}].key`) }}</div>
      </div>

      <div>
        <input placeholder="Valeur" type="text" rows="1" v-model="item.value.value" />
        <div style="color: red">{{ getFieldError(`rows[${index}].value`) }}</div>
      </div>

      <div>
        <button @click.prevent="remove(index)">🗑️</button>
      </div>
      <hr />
    </div>
    
  </form>
  <button @click.stop="push({ key: '', value: '' })">➕</button>
</template>
<script lang="ts" setup>
import { watchImmediate } from "@vueuse/core";
import { useFieldArray, useForm } from "vee-validate";
import * as yup from "yup";
import { ValidationError } from "yup";


const flatValues = defineModel<
  {
    key: string;
    value: string;
  }[]
>({ default: [] });

const schema = yup.object({
  rows: yup
    .array()
    .of(
      yup.object({
        key: yup.string().required("Requis"),
        value: yup.string(),
      })
    )
    .test(
      "unique-keys",
      "Doit être unique !",
      (list, ctx) => {
        if (!list) return true;
        const seen = new Map<string, number>();
        let hasDup = false;

        const errors: ValidationError[] = [];

        list.forEach((item, idx) => {
          if (seen.has(item.key)) {
            hasDup = true;
            errors.push(
              ctx.createError({
                path: `${ctx.path}[${idx}].key`,
              })
            );
            errors.push(
              ctx.createError({
                path: `${ctx.path}[${seen.get(item.key)!}].key`,
              })
            );
          } else {
            seen.set(item.key, idx);
          }
        });

        if (hasDup) return new ValidationError(errors);

        return true;
      }
    ),
});

const { validate, errors } = useForm({
  validationSchema: schema,
  initialValues: {
    rows: flatValues.value,
  },
});

const { fields, push, remove } = useFieldArray<{
  key: string;
  value: string;
}>("rows");


const getFieldError = (fieldPath: string): string | undefined => {
  return errors.value[fieldPath as keyof typeof errors.value];
};

watchImmediate(
  fields,
  (newVal) => {
    if (newVal) {
      validate().then(({ valid }) => {
        if (valid) {
          flatValues.value = newVal.map((v) => v.value);
        }
      });
    }
  },
  { deep: true, flush: "post" }
);
</script>
