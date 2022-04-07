<template>
  <div>
    <header
      class="flex items-center justify-between flex-1 px-2 py-2 space-x-2"
    >
      <div class="inline-flex items-center space-x-2">
        <ButtonSecondary
          class="tracking-wide !font-bold !text-secondaryDark hover:bg-primaryDark focus-visible:bg-primaryDark uppercase"
          :label="t('app.name')"
          to="/"
        />
      </div>
    </header>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "@nuxtjs/composition-api"
import initializePwa from "~/helpers/pwa"
import { getLocalConfig, setLocalConfig } from "~/newstore/localpersistence"
import { useI18n, useToast } from "~/helpers/utils/composables"

const t = useI18n()

const toast = useToast()

/**
 * Once the PWA code is initialized, this holds a method
 * that can be called to show the user the installation
 * prompt.
 */
const showInstallPrompt = ref(() => Promise.resolve()) // Async no-op till it is initialized

onMounted(() => {
  // Initializes the PWA code - checks if the app is installed,
  // etc.
  showInstallPrompt.value = initializePwa()

  const cookiesAllowed = getLocalConfig("cookiesAllowed") === "yes"
  if (!cookiesAllowed) {
    toast.show(`${t("app.we_use_cookies")}`, {
      duration: 0,
      action: [
        {
          text: `${t("action.learn_more")}`,
          onClick: (_, toastObject) => {
            setLocalConfig("cookiesAllowed", "yes")
            toastObject.goAway(0)
            window.open("https://docs.hoppscotch.io/privacy", "_blank")?.focus()
          },
        },
        {
          text: `${t("action.dismiss")}`,
          onClick: (_, toastObject) => {
            setLocalConfig("cookiesAllowed", "yes")
            toastObject.goAway(0)
          },
        },
      ],
    })
  }
})
</script>
