<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>哔哩哔哩2019DASH直传压制方案筛选工具web版V1.0</span>
        <!-- <span class="font-weight-light">MATERIAL DESIGN</span> -->
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <!-- <v-btn
        text
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
      >
        <span class="mr-2">Latest Release</span>
      </v-btn> -->
    </v-app-bar>

    <v-content>
      <Cards/>
    </v-content>
  </v-app>
</template>

<script>
import Cards from './components/Cards'

export default {
  name: 'App',
  components: {
    Cards,
  },
  data: () => ({
    //
  }),
};
</script>
