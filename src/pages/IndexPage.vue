<template>
  <q-page class="full-width row no-wrap justify-center items-start content-start">
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


    <q-form
      @submit="onSubmit"
      @reset="onReset"
      class="col-md-8 col self-center q-gutter-lg"
      style="overflow: auto;"
      autofocus
    >
      <div class="q-gutter-sm text-center">
        <q-radio keep-color v-model="color" val="" label="Default" color="primary" />
        <q-radio keep-color v-model="color" val="#00fffa" label="Info" color="info" />
        <q-radio keep-color v-model="color" val="#04ff00" label="Success" color="positive" />
        <q-radio keep-color v-model="color" val="#ffe900" label="Warning" color="warning" />
        <q-radio keep-color v-model="color" val="#ff0000" label="Danger" color="negative" />
      </div>

      <q-input
        v-model="title"
        :disable="color === ''"
        filled
        label="Title"
        hint="Title of the message"
      />

      <q-input
        v-model="text"
        filled
        autogrow
        label="Your message"
        hint="Your message for Discord"
        lazy-rules
        :rules="[ val => val && val.length > 0 || 'Please type something']"
      />

      <div class="text-center">
        <q-btn label="Send" type="submit" color="secondary"/>
        <q-btn label="Reset" type="reset" color="secondary" flat class="q-ml-sm" />
        <q-btn dense flat icon="build" @click="alert = true" />
      </div>
    </q-form>

    <q-ajax-bar
      ref="wait"
      position="bottom"
      color="accent"
      size="10px"
      skip-hijack
    />
  </q-page>
</template>

<script>
import { ref } from 'vue'
import { useQuasar } from 'quasar'
import { Webhook, MessageBuilder } from "webhook-discord"

export default {
  setup () {
    const $q = useQuasar()
    const text = ref(null)
    const title = ref(null)
    const color = ref("")
    const wait = ref(null)
    const alert = ref(false)

    const url = ref($q.localStorage.getItem("url"))
    const name = ref($q.localStorage.getItem("name"))
    const translationTable = ref($q.localStorage.getItem("translationTable"))

    let Hook = new Webhook(url.value)


    return {
      text,
      title,
      color,
      wait,
      alert,
      url,
      name,
      translationTable,

      async onSubmit() {
        if (url.value === null || name.value === null) {
          $q.notify({
            message: 'Please fill in the data',
            color: 'negative',
            position: 'top',
            timeout: 5000,
            textColor: 'primary',
            icon: 'cloud_done',
          })
          return
        }

        wait.value.start()

        try {
          let tempString = text.value
          for (const [key, value] of Object.entries(JSON.parse(translationTable.value))) {
            tempString = tempString.replace(key, value);
          }

          switch (color.value) {
            case '':
              await Hook.send(new MessageBuilder().setName(name.value).setText(tempString))
                .catch((e) => {throw e})
              break

            default:
              let message = new MessageBuilder().setName(name.value).addField(title.value, tempString).setColor(color.value)

              let tempPings = ''
              for (const [key, value] of Object.entries(JSON.parse(translationTable.value))) {
                if (text.value.search(key) !== -1) tempPings += `${value}\n`
              }
              if(tempPings !== '') message.setText(tempPings)

              await Hook.send(message)
                .catch((e) => {throw e})
              break
          }

          $q.notify({
            color: 'positive',
            textColor: 'primary',
            icon: 'cloud_done',
            message: 'Sent',
            position: 'top',
            timeout: 5000,
          })

          text.value = null
          color.value = ''
          title.value = null
        } catch (e) {
          $q.notify({
            color: 'negative',
            textColor: 'primary',
            icon: 'warning',
            message: 'Something went wrong, try again. \n Error: ' + JSON.stringify(e),
            position: 'top',
            timeout: 5000,
          })
        } finally {
          wait.value.stop()
        }
      },

      onReset () {
        text.value = null
        title.value = null
        color.value = ''
      },

      onSave () {
        $q.localStorage.set("url", url.value)
        $q.localStorage.set("name", name.value)
        $q.localStorage.set("translationTable", translationTable.value)
      }
    }
  }
}
</script>
