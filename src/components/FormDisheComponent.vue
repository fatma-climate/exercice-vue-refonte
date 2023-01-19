<script setup lang="ts">
import { reactive } from 'vue';
import { useQuasar } from 'quasar';
import { useDishesStore } from '../stores/DishesStore';
import { Dishe } from '../types';

const props = defineProps<{
    action: string,
    infos?: Dishe
}>();

const dishesStore = useDishesStore();
const $q = useQuasar()

const initFormData = (): Dishe => {
    let disheObj = {
        id: 0,
        name: '',
        description: '',
        note: 1,
        image: ''
    }
    if (props.infos && Object.keys(props.infos).length !== 0) {
        disheObj = Object.assign(disheObj, props.infos)
    }
    return disheObj
}

let dishe = reactive<Dishe>(initFormData());

function validateMaxLength(value: string, length: number): boolean {
  return !(value.length > length);
}

const emit = defineEmits<{
    (event: 'close-dialog'): void;
}>();

function onSubmit() {
    let message = '';
    if (props.action === 'add') {
        dishesStore.addDishe(dishe)
        message = 'Plat ajouté avec succès'
    } else if (props.action === 'modifier') {
        dishesStore.updateDishe(dishe)
        message = 'Plat mis à jour avec succès';
    }
    emit('close-dialog')
    $q.notify({
        color: 'green-4',
        textColor: 'white',
        icon: 'cloud_done',
        message: message
    })
}
</script>

<template>
    <q-card class="form-card">
        <q-card-section>
            <div class="text-h6 heading">{{ action }} Plat</div>
        </q-card-section>
  
        <q-form @submit.prevent="onSubmit">
            <q-card-section>
                <div class="row q-mb-md">
                    <q-input 
                        filled 
                        label="Nom (Burger)" 
                        class="col" 
                        v-model="dishe.name"
                        :rules="[
                            (val: string) => (val && val.length > 0) || 'Le nom est requis',
                            (val: string) => validateMaxLength(val, 20) || 'Doit contenir au maximum 20 caractères.'
                        ]"/>
                </div>
            
                <div class="row q-mb-md">
                    <q-input
                        filled
                        v-model="dishe.description"
                        label="Description"
                        type="textarea"
                        class="col" 
                        :rules="[
                            (val: string) => validateMaxLength(val, 135) || 'Doit contenir au maximum 135 caractères.'
                        ]"
                    />
                </div>
            
                <div class="row q-mb-md">
                    <q-input
                        filled
                        v-model="dishe.image"
                        label="URL de l'image"
                        class="col"
                    />
                    <q-img
                        :src="dishe.image ? dishe.image : 'src/statics/image-placeholder.png'"
                        class="q-ml-sm"
                        contain
                    />
                </div>
            
                <div class="q-mb-md">
                    <div class="row">
                        <p class="q-mb-none">Note:</p>
                    </div>
                    <div class="row">
                        <q-rating v-model="dishe.note" size="2em" color="orange" />
                    </div>
                </div>
            </q-card-section>
    
        <q-card-actions align="right">
            <q-btn label="Annuler" color="grey" v-close-popup />
            <q-btn label="Sauver" type="submit" color="primary"/>
        </q-card-actions>
      </q-form>
    </q-card>
</template>
  
<style>
.form-card {
    min-width: 400px;
}
.form-card .heading {
    text-transform: capitalize;
}
.form-card .q-card-section {
    width: 100%;
}
.thumbnail {
    max-width: 50px;
    max-height: 50px;
}
.form-card .q-img {
    height: 56px;
    width: 56px;
    border-radius: 10px;
}
.form-card .q-img__image {
    background-size: cover !important;
}
.form-card .q-rating__icon {
    opacity: 0.2;
}
.form-card .q-rating__icon--active {
    opacity: 1;
}
</style>
  