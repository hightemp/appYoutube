<template>
  <q-layout view="lHh Lpr lFf">
    <q-header>
      <q-tabs
        v-model="iTopMenuSelectedTabID"
        class="col-auto"
      >
        <q-route-tab to="/" icon="home" />
        <q-route-tab to="/search" icon="search" />
      </q-tabs>
    </q-header>
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { openURL } from 'quasar'

export default {
  name: 'MyLayout',

  data () {
    return {
      iTopMenuSelectedTabID: 'search',
    }
  },

  methods: {
    openURL
  }
}
</script>

<style>
</style>
