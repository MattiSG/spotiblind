<template>
  <div class="navbar">
    <app-button dark tile class="navbar__brand" to="/">
      <img :src="spotiblindLogoUrl" width="40" height="40" alt="Logo" style="margin-right: 8px">
      SpotiBlind
    </app-button>

    <div class="u-spacer" />

    <app-button dark tile title="Settings" to="/settings">
      <icon-mdi-cog class="navbar__icon" />
    </app-button>
    <app-button dark tile title="Log out" @click="logout()">
      <icon-mdi-logout class="navbar__icon" />
    </app-button>
  </div>

  <div class="content">
    <router-view v-if="authenticated" />
  </div>

  <div v-if="!spotifyClient.deviceReady.value" class="no-devices-found-banner">
    No Spotify devices found!
  </div>
</template>

<script lang="ts" setup>
import { inject, onUnmounted, ref } from 'vue'
import { SPOTIFY_CLIENT } from '../injects'
import spotiblindLogoUrl from '../assets/spotiblind-logo.svg'
import { useRouter } from 'vue-router'

const router = useRouter()
const spotifyClient = inject(SPOTIFY_CLIENT)!

const authenticated = ref(false)

if (!spotifyClient.isLoggedIn()) {
  await router.push('/login')
} else {
  authenticated.value = true
  spotifyClient.start()

  onUnmounted(() => {
    spotifyClient.stop()
  })
}

async function logout () {
  spotifyClient.logout()
  await router.push('/login')
}
</script>

<style lang="sass">
.navbar
  display: flex
  background-color: #7c4dff
  box-shadow: 0 2px 4px -1px rgb(0 0 0 / 20%), 0 4px 5px 0 rgb(0 0 0 / 14%), 0 1px 10px 0 rgb(0 0 0 / 12%)
  height: 48px

  &__brand
    font-size: 1.5em

  &__icon
    font-size: 2em

.content
  flex: 1
  overflow: auto
  padding: .5em

.no-devices-found-banner
  background-color: #ff3f3f
  text-align: center
  color: white
  padding: .5em 0
</style>
