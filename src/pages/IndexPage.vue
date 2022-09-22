<template>
  <q-page class="flex flex-center">
    <div>
      <div class="row justify-between q-my-md">
        <p class="text-h5">Empresas</p>
        <q-btn
          no-caps
          color="primary"
          label="Nova Empresa"
          @click="companyDialog = true"
        />
      </div>
      <q-table :rows="rows" :columns="columns" row-key="name" />
    </div>

    <q-dialog v-model="companyDialog">
      <q-card>
        <q-card-section class="row items-center q-pb-none">
          <div class="text-h6">Cadastrar nova empresa</div>
          <q-space />
          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>

        <q-card-section>
          <q-form @submit="onSubmit" class="q-gutter-md">
            <q-input
              filled
              v-model="formData.name"
              label="Razão Social"
              hint="Nome da empresa"
              lazy-rules
              :rules="[
                (val) =>
                  (val && val.length > 0) || 'Este campo não pode ser vazio',
              ]"
            />
            <q-input
              filled
              v-model="formData.cnpj"
              label="CNPJ"
              mask="##.###.###/####-##"
              lazy-rules
              :rules="[
                (val) =>
                  (val && val.length > 0) || 'Este campo não pode ser vazio',
              ]"
            />
            <q-input
              filled
              v-model="formData.tax_regime"
              label="Regime Tributário"
              lazy-rules
              :rules="[
                (val) =>
                  (val && val.length > 0) || 'Este campo não pode ser vazio',
              ]"
            />
            <q-input
              filled
              type="email"
              v-model="formData.email"
              label="E-mail"
              lazy-rules
              :rules="[
                (val) =>
                  (val && val.length > 0) || 'Este campo não pode ser vazio',
              ]"
            />
            <q-input
              filled
              v-model="formData.site"
              label="Site"
              lazy-rules
              :rules="[
                (val) =>
                  (val && val.length > 0) || 'Este campo não pode ser vazio',
              ]"
            />

            <div>
              <q-btn label="Submit" type="submit" color="primary" />
              <q-btn
                label="Reset"
                type="reset"
                color="primary"
                flat
                class="q-ml-sm"
              />
            </div>
          </q-form>
        </q-card-section>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import { ref, onMounted } from 'vue'
import { useQuasar } from 'quasar'
const columns = [
  {
    name: 'name',
    required: true,
    label: 'Razão social',
    align: 'left',
    field: (row) => row.name,
    format: (val) => `${val}`,
    sortable: true
  },
  {
    name: 'cnpj',
    align: 'center',
    label: 'CNPJ',
    field: 'cnpj',
    sortable: true
  },
  {
    name: 'tax_regime',
    label: 'Tipo de Regime Tributário',
    field: 'tax_regime',
    sortable: true,
    aling: 'center'
  },
  { name: 'email', label: 'E-mail', field: 'email' },
  { name: 'site', label: 'Site', field: 'site' }
]
export default {
  name: 'IndexPage',
  setup () {
    const $q = useQuasar()
    const formData = ref({})
    const companyDialog = ref(false)
    const rows = ref([])
    function onSubmit () {
      const company = formData.value
      console.log(company)
      rows.value.push(company)
      fetch('http://localhost:3000/empresas', {
        method: 'POST',
        body: JSON.stringify(company),
        headers: {
          'Content-Type': 'application/json'
        }
      })
        .then((response) => {
          if (response.status === 201) {
            $q.notify({
              message: 'Empresa cadastrada com sucesso',
              position: 'top',
              color: 'positive'
            })
          }
        })
        .catch((error) => {
          console.error(error)
          $q.notify({
            message: 'Erro ao cadastrar empresa',
            position: 'top',
            color: 'negative'
          })
        })
      formData.value = {}
      companyDialog.value = false
    }
    onMounted(() => {
      fetch('http://localhost:3000/empresas')
        .then((response) => response.json())
        .then((data) => {
          rows.value = data
        })
        .catch((error) => {
          console.error(error)
          $q.notify({
            message: 'Erro ao buscar empresas',
            position: 'top',
            color: 'negative'
          })
        })
    })
    return {
      onSubmit,
      formData,
      companyDialog,
      columns,
      rows
    }
  }
}
</script>
