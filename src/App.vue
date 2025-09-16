<script setup lang="ts">
import Interdependent from './Interdependent.vue';
import NoDuplication from './NoDuplication.vue';
import { ref } from 'vue';
import Simple from './Simple.vue';
import File from './File.vue';
import FileArray from './FileArray.vue';

const current = ref(0);
</script>

<template>
  <button @click="current = 1">Simple</button>
  <button @click="current = 2">Interdependent</button>
  <button @click="current = 3">No Duplication</button>
  <button @click="current = 4">File</button>
  <button @click="current = 5">File Array</button>
  <hr>
  <Simple v-if="current === 1" />
  <Interdependent v-if="current === 2" />
  <NoDuplication v-if="current === 3" />
  <File v-if="current === 4" />
  <FileArray v-if="current === 5" />
</template>

<style>
* {
  font-family: sans-serif;
}
</style>
