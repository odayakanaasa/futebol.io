<template>
  <div class="container">
		<div class="arrows is-left" :class="{'is-invisible': !arrowLeft}" @mousedown="scrollLeft">
			<figure class="image is-16x16">
				<img src="/static/left.svg" alt="Left arrow">
			</figure>
		</div>
		<div class="arrows is-right" :class="{'is-invisible': !arrowRight}" @mousedown="scrollRight">
			<figure class="image is-16x16">
				<img src="/static/right.svg" alt="Right arrow">
			</figure>
		</div>
    <div class="columns is-mobile is-vcentered" ref="leagues" @scroll="scroll">
      <div class="column is-light is-one-fifth" :data-league="league.path" ref="league" @click="scrollTo;setLeague($route.params.league)" v-for="league in leagues" :key="league.path" :class="{'is-selected': isSelected(league)}">
        <router-link :to="{name: 'league', params: {league: league.path}}">
          <figure class="image is-32x32">
            <img :src="'/static/'+league.icon+'.svg'" :alt="league.country">
          </figure>
          {{league.name}}
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
import { mapActions } from 'vuex'
import leagues from '@/leagues.json'

export default {
  data() {
    return {
      leagues: leagues,
      arrowLeft: false,
      arrowRight: true
    }
  },
  mounted() {
    this.setLeague(this.$route.params.league || '')
    this.scrollTo()
  },
  methods: {
    ...mapActions(['setLeague']),
    isSelected(league) {
      return league.path == this.$route.params.league
    },
    scrollTo() {
      let el = this.$refs.league.find(
        league => league.dataset.league == this.league
      )
      if (el)
        this.$refs.leagues.scrollTo(
          el.offsetLeft -
            this.$refs.leagues.clientWidth / 1.9 +
            el.clientWidth / 2,
          0
        )
    },
    scroll() {
      this.arrowLeft = this.$refs.leagues.scrollLeft > 0
      this.arrowRight =
        this.$refs.leagues.scrollLeft <
        this.$refs.leagues.scrollWidth - this.$refs.leagues.clientWidth
    },
    scrollLeft() {
      if (this.$refs.leagues.scrollLeft == 0) return
      this.$refs.leagues.scrollLeft -= 180
    },
    scrollRight() {
      if (
        this.$refs.leagues.scrollLeft ==
        this.$refs.leagues.scrollWidth - this.$refs.leagues.clientWidth
      )
        return
      this.$refs.leagues.scrollLeft += 180
    }
  },
  computed: {
    league() {
      return this.$store.state.league
    }
  },
  watch: {
    league() {
      this.scrollTo()
    },
    $route(to, from) {
      let league = this.leagues.find(league => league.path == this.league)
      if (to.name == 'league' && league)
        document.title = `futebol.io | ${league.name}`
      else document.title = 'futebol.io | tabelas de futebol'
    }
  }
}
</script>
<style lang="scss" scoped>
@import '~bulma/sass/utilities/_all';
.container {
  overflow: hidden;
  margin-top: 25px;
  padding: 0 25px;
}
.arrows {
  position: absolute;
  top: 35px;
  z-index: 1;
  padding: 0 5px;
  img {
    max-height: 20px;
    opacity: 0.4;
  }
  &.is-left {
    left: 0;
  }
  &.is-right {
    right: 0;
  }
}
.columns {
  width: 100%;
  height: 100%;
  overflow: auto;
  padding-bottom: 10px;
  margin: 0;
  .column {
    min-width: 180px;
    margin: 0 10px;
    padding-bottom: 1.25rem;
    a {
      transition: all 0.1s ease-out;
    }
    &.is-selected {
      a {
        color: #00c853;
      }
    }
    &:active,
    &:hover {
      a {
        transform: translateY(-4px);
      }
    }
  }

  a {
    display: block;
    text-align: center;
    font-weight: bold;
    color: $text;
    white-space: nowrap;
    .image {
      margin: 0 auto;
    }
  }

  &:last-child {
    margin-bottom: -1.6rem;
  }
}
</style>
