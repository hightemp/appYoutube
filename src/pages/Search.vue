<template>
  <q-page class="fit column wrap justify-start items-start content-start" style="height:calc(100vh - 48px)">  
    <q-splitter
      v-model="iSplitterModel"
      style="height:calc(100vh - 48px)"
    >
      <template v-slot:before>
        <div class="q-pa-none">
            <q-input v-model="sSearchText" label="Search" dense @keyup.enter="fnSearch">
                <template v-slot:append>
                    <q-icon v-if="sSearchText !== ''" name="close" @click="sSearchText = ''" class="cursor-pointer" />
                    <q-icon name="search" />
                </template>
            </q-input>
            
            <q-list dense bordered separator>
                <q-item 
                  clickable 
                  v-ripple 
                  class="" 
                  style="padding-right: 5px"
                  v-for="(oItem, sKey) in oSearchedResults"
                  :active="sKey==sActiveItem"
                  active-class="active-menu-item"
                  @click="fnSelect(sKey)"
                >
                    <q-item-section>
                        <q-item-label>{{ sKey }}</q-item-label>
                    </q-item-section>
                    <q-item-section side>
                        <div class="q-gutter-xs">
                            <q-badge :label="oItem.aResults.length" />
                            <q-btn 
                              class="gt-xs" 
                              size="12px" 
                              flat 
                              dense 
                              icon="close"
                              @click="fnDelete(sKey)" 
                            />
                        </div>
                    </q-item-section>
                </q-item>
            </q-list>
        </div>
      </template>

      <template v-slot:after>
        <q-list
          v-if="oActiveItem" 
          dense 
          separator 
          class="fill-width"
        >
            <q-item 
              clickable 
              v-ripple 
              v-for="oItem in oActiveItem.aResults" 
              class="fill-width"
            >
                <q-item-section top thumbnail class="fill-width q-pa-sm">
                    <img :src="oItem.thumbnails.default.url">
                </q-item-section>
                <q-item-section>
                    <q-item-label>{{ oItem.title }}</q-item-label>
                    <q-item-label caption>{{ oItem.description }}</q-item-label>
                </q-item-section>
                <q-item-section side>
                    {{ fnFormatDate(oItem.datePublished) }}
                </q-item-section>    
            </q-item>
        </q-list>
      </template>

    </q-splitter>   
  </q-page>
</template>

<style>
</style>

<script>

import Vue from 'vue'
const { YouTube } = require('better-youtube-api')
import moment from 'moment'

export default {
  name: 'PageSearch',

  computed: {
    oActiveItem()
    {
      var oThis = this

      return oThis.oSearchedResults[oThis.sActiveItem]
    }
  },

  methods: {
    fnSelect(sKey)
    {
      this.sActiveItem = sKey
      this.fnUpdate()
    },
    fnDelete(sKey)
    {
      delete this.oSearchedResults[sKey]
    },
    fnSearch()
    {
      console.log('fnSearch')
      Vue.set(this.oSearchedResults, this.sSearchText, {
        oOptions: {},
        aResults: []
      })
      this.sActiveItem = this.sSearchText
      console.log(this.oSearchedResults, this.sActiveItem)
      this.fnUpdate()
    },
    fnUpdate()
    {
      console.log('fnUpdate')

      var oThis = this

      if (!oThis.sActiveItem)
        return;

      oThis
        .oYoutube
        .searchVideos(oThis.sActiveItem)
        .then((aResults) => Vue.set(oThis.oSearchedResults[oThis.sActiveItem], 'aResults', aResults))
    },
    fnFormatDate(oDate)
    {
        return moment(oDate).format("hh:mm:ss DD.MM.YY")
    }
  },

  data()
  {
    return {
      iSplitterModel: 30,
      sSearchText: '',
      sActiveItem: '',
      oYoutube: new YouTube("AIzaSyCFH7bDEOI0J6iZG7Cg5w59Mwjdkjvk4Gk", { cache: false }),
      oSearchedResults: {}
    }
  },

  mounted()
  {
    this.fnUpdate()
  }
}
</script>
