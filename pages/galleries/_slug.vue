<template>
  <div>
    <client-only>
      <v-container class="pt-5">
        <page-title
          :text="pageTitle"
          :back-slug="backSlug" />
      </v-container>
      <v-container>
        <client-only>
          <v-row
            v-masonry
            class="mx-auto"
            fit-width="true"
            gutter="10"
            transition-duration="0.2s"
            item-selector=".child">
            <div
              v-for="(image, index) in gallery.images"
              :key="index"
              v-masonry-tile
              class="child">
              <image-card
                btn-text="View"
                :item="image"
                item-type="images" />
            </div>
          </v-row>
        </client-only>
      </v-container>
      <v-container>
        <v-row
          align="center"
          class="prev-next-wrapper">
          <v-btn
            text
            :ripple="false"
            small
            nuxt
            :to="prevSlug"
            :disabled="!prevSlug"
            color="info">
            <icon
              :disabled="!prevSlug"
              name="angle-left"
              fill="grey"
              height="20px"
              width="20px" />
            Previous Gallery
          </v-btn>
          <v-btn
            text
            :ripple="false"
            small
            nuxt
            :to="nextSlug"
            :disabled="!nextSlug"
            color="info">
            Next Gallery
            <icon
              :disabled="!nextSlug"
              name="angle-right"
              fill="grey"
              height="20px"
              width="20px" />
          </v-btn>
        </v-row>
      </v-container>
    </client-only>
  </div>
</template>

<script>
  import ImageCard from '~/components/ImageCard';
  import PageTitle from '~/components/PageTitle';
  import fetchGalleries from '~/mixins/fetchGalleries';

  export default {
    name: 'GallerySlug',

    mixins: [fetchGalleries],

    transition: 'page-fade',

    data: () => ({
      options: {
        columnWidth: 400,
      },
    }),

    computed: {
      backSlug() {
        return '/portfolio';
      },
      fetchGalleryNavigationSlugs() {
        let steps = { next: null, previous: null };

        const currentGalleryIndex = this.galleries.indexOf(this.gallery);

        // if a next gallery exists
        if (this.galleries[currentGalleryIndex + 1]) {
          steps['next'] = `/galleries/${this.galleries[currentGalleryIndex + 1].slug}`;
        }

        // if a previous gallery exists
        if (this.galleries[currentGalleryIndex - 1]) {
          steps['previous'] = `/galleries/${this.galleries[currentGalleryIndex - 1].slug}`;
        }

        return steps;
      },
      gallery() {
        return this.galleries.find(gallery => gallery.slug === this.$route.params.slug);
      },
      pageTitle() {
        return this.gallery.title;
      },
      nextSlug() {
        return this.fetchGalleryNavigationSlugs.next;
      },
      prevSlug() {
        return this.fetchGalleryNavigationSlugs.previous;
      }
    },

    components: {
      ImageCard,
      PageTitle,
    },

    head() {
      return {
        script: [
          {
            async: true,
            defer: true,
            src: 'https://cdnjs.cloudflare.com/ajax/libs/masonry/4.0.0/masonry.pkgd.min.js'
          }
        ],
        title: this.pageTitle
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import '~/css/breakpoints';

  .prev-next-wrapper {
    display: flex;
    justify-content: space-between;

    .v-btn {
      font-size: 1.2rem;
    }
  }
</style>