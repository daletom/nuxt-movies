
<template>
  <div class="spacing" :class="$style.info">
    <div :class="$style.left">
      <div :class="$style.poster">
        <nuxt-img
          provider="imgix"
          :modifiers="{ auto : 'format,compress', ch: 'dpr,width' }"
          v-if="poster"
          width="370"
          height="556"
          sizes="large:20vw xlarge:20vw xlarge1:18vw xlarge3:780"
          :alt="name"
          :src="poster" />
        <PlaceholderIcon v-else />
      </div>
    </div>

    <div :class="$style.right">
      <div
        v-if="item.overview"
        :class="$style.overview">
        <h2 :class="$style.title">
          Storyline
        </h2>

        <!-- eslint-disable-next-line vue/no-v-html -->
        <div v-html="item.overview" />
      </div>

      <div :class="$style.stats">
        <ul class="nolist">
          <li v-if="item.first_air_date">
            <div :class="$style.label">
              First Aired
            </div>

            <div :class="$style.value">
              {{ item.first_air_date | fullDate }}
            </div>
          </li>
          <li v-if="item.last_air_date">
            <div :class="$style.label">
              Last Aired
            </div>

            <div :class="$style.value">
              {{ item.last_air_date | fullDate }}
            </div>
          </li>
          <li v-if="item.episode_run_time && item.episode_run_time.length">
            <div :class="$style.label">
              Runtime
            </div>

            <div :class="$style.value">
              {{ formatRunTime(item.episode_run_time) }}
            </div>
          </li>
          <li v-if="creators">
            <div :class="$style.label">
              Creator
            </div>

            <!-- eslint-disable-next-line vue/no-v-html -->
            <div :class="$style.value" v-html="creators" />
          </li>
          <li v-if="item.genres && item.genres.length">
            <div :class="$style.label">
              Genre
            </div>

            <!-- eslint-disable-next-line vue/no-v-html -->
            <div :class="$style.value" v-html="formatGenres(item.genres)" />
          </li>
          <li v-if="item.number_of_seasons">
            <div :class="$style.label">
              Seasons
            </div>

            <div :class="$style.value">
              {{ item.number_of_seasons }}
            </div>
          </li>
          <li v-if="item.number_of_episodes">
            <div :class="$style.label">
              Episodes
            </div>

            <div :class="$style.value">
              {{ item.number_of_episodes }}
            </div>
          </li>
          <li v-if="item.status">
            <div :class="$style.label">
              Status
            </div>

            <div :class="$style.value">
              {{ item.status }}
            </div>
          </li>
          <li v-if="item.original_language">
            <div :class="$style.label">
              Language
            </div>

            <div :class="$style.value">
              {{ item.original_language | fullLang }}
            </div>
          </li>
          <li v-if="item.networks && item.networks.length">
            <div :class="$style.label">
              Network
            </div>

            <div :class="$style.value">
              {{ item.networks | arrayToList }}
            </div>
          </li>
        </ul>
      </div>

      <div :class="$style.external">
        <ExternalLinks :links="item.external_ids" />
      </div>
    </div>
  </div>
</template>

<script>
import { name, creators } from '~/mixins/Details';
import ExternalLinks from '~/components/ExternalLinks';
import PlaceholderIcon from '~/assets/images/placeholder.svg?inline';

export default {
  components: {
    ExternalLinks,
    PlaceholderIcon
  },

  mixins: [
    name,
    creators
  ],

  props: {
    item: {
      type: Object,
      required: true
    }
  },

  computed: {
    poster() {
      return this.item.poster_path;
    }
  },

  created() {
    if (this.item.homepage) {
      // TODO: avoid mutating props
      // eslint-disable-next-line vue/no-mutating-props
      this.item.external_ids.homepage = this.item.homepage;
    }
  },

  methods: {
    formatGenres(genres) {
      return genres.map(genre => `<a href="/genre/${genre.id}/tv">${genre.name}</a>`).join(', ');
    },

    formatRunTime(times) {
      return times.map(time => `${time}m`).join(', ');
    }
  }
};
</script>

<style lang="scss" module>
@import '~/assets/css/utilities/_variables.scss';

.info {
  @media (min-width: $breakpoint-medium) {
    display: flex;
  }
}

.left {
  display: none;

  @media (min-width: $breakpoint-medium) {
    display: block;
    width: 25%;
    max-width: 400px;
    padding-right: 3rem;
  }

  @media (min-width: $breakpoint-large) {
    padding-right: 5rem;
  }
}

.right {
  @media (min-width: $breakpoint-medium) {
    flex: 1;
  }
}

.poster {
  background-color: $secondary-color;

  // TODO: should add styling for span element

  span {
    display: flex;
    align-items: center;
    justify-content: center;
  }
}

.overview {
  max-width: 1000px;
  margin-bottom: 3rem;
  font-size: 1.5rem;
  color: $text-color;

  @media (min-width: $breakpoint-large) {
    font-size: 1.6rem;
  }
}

.title {
  margin-bottom: 1rem;
  font-size: 1.8rem;
  color: #fff;
  letter-spacing: $letter-spacing;

  @media (min-width: $breakpoint-large) {
    font-size: 2.4rem;
  }
}

.stats {
  margin-bottom: 3rem;
  font-size: 1.5rem;
  color: $text-color;

  @media (min-width: $breakpoint-large) {
    font-size: 1.6rem;
  }

  ul {
    @media (min-width: $breakpoint-medium) {
      display: flex;
      flex-wrap: wrap;
    }
  }

  li {
    display: flex;
    padding: 0.2rem 0;

    @media (min-width: $breakpoint-medium) {
      width: 50%;
    }

    @media (min-width: $breakpoint-xlarge) {
      width: 100%;
    }
  }

  a {
    color: $primary-color;
    text-decoration: underline;
  }
}

.label {
  flex: 1;
  max-width: 90px;
  margin-right: 1.5rem;
  color: #fff;

  @media (min-width: $breakpoint-xsmall) {
    max-width: 110px;
  }
}

.value {
  flex: 2;
}

.external {
  ul {
    display: flex;
    margin-left: -0.5rem;
  }

  a {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 4.4rem;
    height: 4.4rem;

    svg {
      transition: all 0.3s ease-in-out;
    }

    &:hover,
    &:focus {
      svg {
        fill: $primary-color;
      }
    }
  }
}
</style>

