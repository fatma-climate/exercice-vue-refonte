<script setup lang="ts">
import { computed, ref } from 'vue';
import { Dishe } from '../types';
import { useDishesStore } from '../stores/DishesStore';
import DisheComponent from 'src/components/DisheComponent.vue';
import AddButtonComponent from 'src/components/AddButton.vue';
import FormDisheComponent from 'src/components/FormDisheComponent.vue';


const showFormDishe = ref(false)

const dishesStore = useDishesStore();
const dishes = computed(() : Dishe[] => dishesStore.getDishes)
</script>

<template>
  <q-page class="q-pa-lg">
    <div class="row q-gutter-lg">
      <dishe-component v-for="dishe in dishes" :key="dishe.id" :dishe="dishe" />

      <add-button-component @click="showFormDishe = true" />

      <q-dialog v-model="showFormDishe">
        <form-dishe-component action="add" @close-dialog="showFormDishe = false"/>
      </q-dialog>
    </div>
  </q-page>
</template>
  