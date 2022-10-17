<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar class="q-electron-drag">
        <q-toolbar-title class="text-start">
          Webhook creator
        </q-toolbar-title>

        <q-space />
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'MainLayout',

  setup () {
    const $q = useQuasar()
    const url = ref($q.localStorage.getItem("url"))
    const name = ref($q.localStorage.getItem("name"))
    const translationTable = ref($q.localStorage.getItem("translationTable"))

    return {
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
    }
  }
})
</script>
