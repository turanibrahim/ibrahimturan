<template>
  <div v-if="!loading">
    <v-timeline
      :class="$nuxt.$vuetify.breakpoint.mdAndUp ? 'px-3' : 'pr-2'"
      :dense="$nuxt.$vuetify.breakpoint.smAndDown"
    >
      <v-timeline-item
        v-for="item in experiences"
        :key="item.id"
        :color="setColor(item.id)"
        small
      >
        <template v-slot:opposite>
          <span :class="`headline font-weight-bold ${setColor(item.id)}--text`">
            {{ $nuxt.$moment(item.startingDate).format('MMMM YYYY') }}
            ~
            {{
              item.terminationDate
                ? $nuxt.$moment(item.terminationDate).format('MMMM YYYY')
                : $t('experience.current')
            }}
          </span>
        </template>
        <template v-if="item.logo" v-slot:icon>
          <v-avatar size="75">
            <img :src="item.logo" />
          </v-avatar>
        </template>
        <div class="py-4">
          <h2 :class="`headline font-weight-bold ${setColor(item.id)}--text`">
            {{ item.title }}
          </h2>
          <h4 :class="`subtitle-1 mt-0 pt-0 mb-4 ${setColor(item.id)}--text`">
            {{ item.company }}
          </h4>
          <div v-html="item.description"></div>
        </div>
      </v-timeline-item>
    </v-timeline>
  </div>
  <div v-else class="fill-height">
    <v-row class="fill-height" justify="center" align="center" no-gutters>
      <v-col cols="12">
        <div class="text-center">
          <v-progress-circular
            :size="70"
            :width="7"
            color="primary"
            indeterminate
          ></v-progress-circular>
        </div>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import { mapMutations, mapState, mapActions } from 'vuex'

export default {
  data() {
    return {
      colors: [
        'red',
        'green',
        'blue-grey',
        'pink',
        'lime',
        'yellow',
        'brown',
        'purple',
        'brown',
        'pink',
        'light-green',
        'brown',
        'deep-purple',
        'deep-orange',
        'indigo',
        'blue',
        'amber',
        'light-blue',
        'cyan',
        'orange',
        'teal'
      ],
      loading: true
    }
  },
  computed: {
    ...mapState({
      locale: (state) => state.locale,
      experiences: (state) => state.experience.experiences,
      metaData: (state) => state.layout.metaData
    })
  },
  watch: {
    async locale(newLocale, oldLocale) {
      this.setPageTitle({ title: this.$t('titles.experience') })
      try {
        this.loading = true
        await this.fetchMetaData({ path: 'experience', lang: this.locale })
        await this.fetchExperiences()
      } finally {
        this.loading = false
      }
    }
  },
  async created() {
    this.setPageTitle({ title: this.$t('titles.experience') })
    this.setPageTitleImage('/img/6.jpg')
    try {
      this.loading = true
      await this.fetchMetaData({ path: 'experience', lang: this.locale })
      await this.fetchExperiences()
    } finally {
      this.loading = false
    }
  },
  methods: {
    ...mapMutations({
      setPageTitle: 'layout/setPageTitle',
      setPageTitleImage: 'layout/SET_PAGE_TITLE_IMAGE'
    }),
    ...mapActions({
      fetchExperiences: 'experience/fetchExperiences',
      fetchMetaData: 'layout/fetchMetaData'
    }),
    setColor(id) {
      return this.colors[this.colors.length % id]
    }
  },
  head() {
    return {
      title: this.metaData.title,
      meta: [
        // hid is used as unique identifier. Do not use `vmid` for it as it will not work
        {
          hid: this.metaData.hid,
          name: this.metaData.name,
          content: this.metaData.description
        }
      ]
    }
  }
}
</script>
