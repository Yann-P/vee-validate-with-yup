<template>
  <h3>Champs interdépendants</h3>

  <form>
    Numéro de sécu
    <input type="text" v-model="numSecu" />
    <div style="color: red">{{ errors.numSecu }}</div>

    <br /><br />

    Sexe
    <select v-model="sexe">
      <option value="Homme">Homme</option>
      <option value="Femme">Femme</option>
    </select>
    <div style="color: red">{{ errors.sexe }}</div>

    <br /><br />

    Année de naissance
    <input type="text" v-model="anneeNaissance" />
    <div style="color: red">{{ errors.anneeNaissance }}</div>
  </form>
</template>

<script lang="ts" setup>
import { useForm } from "vee-validate";
import * as yup from "yup";
import { ValidationError } from "yup";

const schema = yup
  .object({
    numSecu: yup.string().required("Requis"),
    sexe: yup.string().required("Requis"),
    anneeNaissance: yup.string().required("Requis").test("age", "L'année doit être supérieure à 1920", validateAnneeNaissance),
  })
  .test(
    "Coherence sexe",
    "Incohérence entre numéro de sécu et sexe",
    (data, ctx) => {
      if (isSexeIncoherent(data.numSecu, data.sexe)) {
        return new ValidationError([
          ctx.createError({ path: "numSecu" }),
          ctx.createError({ path: "sexe" })
        ]);
      }
    }
  )
  .test(
    "Coherence année de naissance",
    "Incohérence entre numéro de sécu et année de naissance",
    (data, ctx) => {
      if (isBirthYearIncoherent(data.numSecu, data.anneeNaissance)) {
        return new ValidationError([
          ctx.createError({ path: "numSecu" }),
          ctx.createError({ path: "anneeNaissance" })
        ]);
      }
    }
  );

const { errors, defineField } = useForm({
  validationSchema: schema,
  validateOnMount: true,
  initialValues: {
    numSecu: "2 58 11 ...",
    sexe: "Femme",
    anneeNaissance: "1990",
  },
});


const [numSecu] = defineField("numSecu");
const [sexe] = defineField("sexe");
const [anneeNaissance] = defineField("anneeNaissance");

function isSexeIncoherent(numSecu: string, sexe: string) {
  return (numSecu.startsWith("1") && sexe === "Femme") ||
         (numSecu.startsWith("2") && sexe === "Homme");
}

function isBirthYearIncoherent(numSecu: string, anneeNaissance: string) {
  return (numSecu ?? '').replace(/ /g, "").substring(1, 3) !== anneeNaissance.substring(2, 4);
}

function validateAnneeNaissance(value: string) {
  return !isNaN(+value) && +value > 1920;
}
</script>
