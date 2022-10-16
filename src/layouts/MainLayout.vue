<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar class="q-electron-drag">
        <q-toolbar-title class="text-start">
          Webhook creator
        </q-toolbar-title>

        <q-space />
        <q-btn dense flat icon="build" @click="alert = true" />
<!--        <q-btn v-if="$q.platform.is.electron" dense flat icon="minimize" @click="minimize" />-->
<!--        <q-btn v-if="$q.platform.is.electron" dense flat icon="crop_square" @click="toggleMaximize" />-->
<!--        <q-btn v-if="$q.platform.is.electron" dense flat icon="close" @click="closeApp" />-->
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>

  <q-dialog v-model="alert">
    <q-card class="full-width">
      <q-card-section>
        <div class="text-h6">Data</div>
      </q-card-section>

      <q-card-section class="q-pt-none">
        <q-form
          @submit="onSave"
          class="col-md-8 col self-center q-gutter-lg"
          style="overflow: auto;"
          autofocus
        >
          <q-input
            v-model="url"
            filled
            autogrow
            label="URL of webhook"
            hint="URL of your Discord webhook"
            lazy-rules
            :rules="[ val => val && val.length > 0 || 'Please type something']"
          />

          <q-input
            v-model="name"
            filled
            autogrow
            label="Webhook name"
            hint="Name of your Discord webhook"
            lazy-rules
            :rules="[ val => val && val.length > 0 || 'Please type something']"
          />

          <q-input
            v-model="translationTable"
            filled
            autogrow
            label="Table of translations"
            hint="Table that changes one text to another. For example: 'Hello': 'Hi'"
            lazy-rules
            :rules="[ val => val && val.length > 0 || 'Please type something']"
          />

          <div class="text-center">
            <q-btn label="Save" type="submit" color="secondary" v-close-popup/>
          </div>
        </q-form>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>

<script>
import { defineComponent, ref } from 'vue'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'MainLayout',

  setup () {
    const $q = useQuasar()
    const alert = ref(false)
    const url = ref($q.localStorage.getItem("url"))
    const name = ref($q.localStorage.getItem("name"))
    const translationTable = ref($q.localStorage.getItem("translationTable"))

    return {
      alert,
      url,
      name,
      translationTable,
      // minimize () {
      //   if (process.env.MODE === 'electron') {
      //     window.myWindowAPI.minimize()
      //   }
      // },
      // toggleMaximize () {
      //   if (process.env.MODE === 'electron') {
      //     window.myWindowAPI.toggleMaximize()
      //   }
      // },
      // closeApp () {
      //   if (process.env.MODE === 'electron') {
      //     window.myWindowAPI.close()
      //   }
      // },

      onSave () {
        $q.localStorage.set("url", url.value)
        $q.localStorage.set("name", name.value)
        $q.localStorage.set("translationTable", translationTable.value)
      }
    }
  }
})
</script>
