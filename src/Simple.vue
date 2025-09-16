<template>
  <div>
    <h3>Basic</h3>
    
    <input placeholder="Email" v-model="email" />
    <div>{{ errors.email }}</div>

    <br /><br />

    <input placeholder="Age" v-model="age" type="number" />
    <div>{{ errors.age }}</div>

  </div>
</template>

<script lang="ts" setup>
    import { useForm } from 'vee-validate'
    import * as yup from 'yup'

    const schema = yup.object({
    email: yup
        .string()
        .required('Email requis')
        .email('Email invalide'),
    age: yup
        .number()
        .required('Âge requis')
        .min(18, 'Âge minimum : 18')
        .max(100, 'Âge maximum : 100')
    })

    const { errors, defineField } = useForm({
    validationSchema: schema
    })

    const [email] = defineField('email')
    const [age] = defineField('age')
</script>
