<script setup lang="ts">
import { computed, ref } from 'vue';
import { useQuasar } from 'quasar';
import { Dishe } from '../types';
import { useDishesStore } from '../stores/DishesStore';
import FormDisheComponent from 'src/components/FormDisheComponent.vue';

const props = defineProps<{
  dishe: Dishe;
}>();

const showFormDishe = ref(false);

const $q = useQuasar();
const dishesStore = useDishesStore();

const disheImage = computed(() =>
  props.dishe && props.dishe.image
    ? props.dishe.image
    : 'src/statics/image-placeholder.png'
);

function deleteDishe() {
  $q.dialog({
    title: 'Supprimer le plat',
    message: 'Voulez-vous vraiement supprimer ce plat ?',
    cancel: 'Annuler',
    ok: 'Supprimer',
    persistent: true,
  }).onOk(() => {
    if (props.dishe.id) {
      dishesStore.deleteDishe(props.dishe.id);
    }
  });
}
</script>

<template>
  <q-card class="card">
    <q-img :src="disheImage" basic contain>
      <div class="absolute-bottom text-h6">
        {{ dishe.name }}
      </div>
    </q-img>

    <q-card-section>
      <q-rating
        :model-value="dishe.note"
        size="2em"
        color="orange"
        readonly
        class="q-mt-sm"
      />
    </q-card-section>

    <q-card-section v-if="dishe.description">
      {{ dishe.description }}
    </q-card-section>
    <q-card-section v-else>
      <i>Aucune description fournie</i>
    </q-card-section>

    <q-card-actions class="absolute-bottom" align="right">
      <q-btn @click="showFormDishe = true" icon="edit" color="blue" flat
        >Modifier</q-btn
      >
      <q-btn @click="deleteDishe()" icon="delete" color="red" flat
        >Supprimer</q-btn
      >
    </q-card-actions>

    <q-dialog v-model="showFormDishe">
      <form-dishe-component
        action="modifier"
        :infos="dishe"
        @close-dialog="showFormDishe = false"
      />
    </q-dialog>
  </q-card>
</template>

<style>
.card {
  min-height: 400px;
  max-width: 250px;
  width: 250px;
  transition: background 0.3s;
}
.card-clickable {
  cursor: pointer;
}
.card-clickable:hover {
  background: #bdbdbd !important;
}
.card .q-img {
  max-height: 180px;
}
.card .q-img__image {
  background-size: cover !important;
}
.card .q-rating__icon {
  opacity: 0.2;
}
.card .q-rating__icon--active {
  opacity: 1;
}
</style>
