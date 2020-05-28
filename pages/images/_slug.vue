<template>
  <div class="page-wrapper">
    <v-container class="pb-0">
      <page-title
        :text="pageTitle"
        back />
    </v-container>
    <v-container
      fluid
      class="px-5">
      <v-row
        align="center"
        justify="center">
        <v-col
          sm="12"
          md="6"
          class="image-container">
          <v-img
            :src="imageSrc"
            max-width="800px"
            @contextmenu.prevent>
            <v-btn
              class="expand-btn"
              icon
              @click.stop="openModal">
              <icon
                name="expand-arrows-alt"
                fill="#fff"
                height="20px"
                width="20px" />
            </v-btn>
          </v-img>
        </v-col>
        <v-col
          sm="12"
          md="6"
          class="details-container">
          <h1 class="display-2 font-weight-bold mb-3">
            Details
          </h1>
          <div
            v-for="(detail, index) in details"
            :key="index"
            class="detail">
            <p class="body-1 font-weight-bold">
              {{ detail.title }}:
            </p>
            <p class="body-1">
              {{ detail.value | strippedTags }}
            </p>
          </div>
          <v-form
            ref="form"
            v-model="valid">
            <v-select
              v-model="chosenSize"
              dense
              :items="availableSizes"
              label="Please choose a size"
              outlined
              required
              :rules="[v => !!v || 'Selection is required']" />
            <v-btn
              depressed
              :ripple="false"
              color="accent"
              class="add-to-cart-btn"
              :disabled="!valid"
              @click="submit">
              Add To Cart
            </v-btn>
          </v-form>
        </v-col>
      </v-row>
    </v-container>
    <v-snackbar
      v-model="snackbar"
      bottom
      left
      color="success"
      :timeout="3000">
      <icon
        name="check-circle"
        fill="white"
        height="30px"
        width="30px" />
      <p class="body-1 mb-0 font-weight-bold">
        Added To Cart
      </p>
      <v-btn
        icon
        @click="snackbar = false">
        <icon
          name="multiply"
          fill="white"
          height="20px"
          width="20px" />
      </v-btn>
    </v-snackbar>
    <full-screen-image
      :image="image"
      :open="isModalOpen"
      @handle-close-dialog="isModalOpen = false" />
  </div>
</template>

<script>
  import { mapMutations } from 'vuex';
  import FullScreenImage from '~/components/modals/FullScreenImage';
  import PageTitle from '~/components/PageTitle';

  export default {
    async asyncData ({ $axios, params, env }) {
      const image = await $axios.$get(`${env.WP_API_URL}/wp/v2/media?slug=${params.slug}`);
      return { image: image[0] };
    },

    data: () => ({
      availableSizes: [
        { text: '500dpi', value: '1' },
        { text: '1000dpi', value: '2' },
        { text: '1500dpi', value: '3' },
        { text: '2000dpi', value: '4' },
        { text: 'Full Resolution', value: '5' },
      ],
      chosenSize: null,
      isModalOpen: false,
      snackbar: false,
      valid: false,
    }),

    computed: {
      details() {
        return [
          { title: 'Title', value: this.image.title.rendered },
          { title: 'Location', value: this.image.acf.location },
          { title: 'Description', value: this.image.caption.rendered }
        ]
      },
      imageSrc() {
        return this.image.media_details.sizes.large.source_url;
      },
      pageTitle() {
        return this.image.title.rendered;
      }
    },

    methods: {
      ...mapMutations('cart', ['addToCart']),

      openModal() {
        this.isModalOpen = true;
      },

      submit() {
        if (this.$refs.form.validate()) {
          this.addToCart({ id: 2, title: 'Titties' });
          this.snackbar = true;
        }
      },
    },

    filters: {
      strippedTags(string) {
        return string.replace(/<\/?[^>]+>/ig, "");
      }
    },

    components: {
      FullScreenImage,
      PageTitle,
    },
  }
</script>

<style lang="scss" scoped>
  @import '~/css/breakpoints';

  .image-container {
    .v-image {
      margin: 0 auto;

      .expand-btn {
        cursor: pointer;
        float: right;
        opacity: 0;
        padding: 1rem;
        transition: 0.2s all ease-in-out;
        visibility: hidden;
      }

      &:hover {
        .expand-btn {
          opacity: 1;
          visibility: visible;
        }
      }
    }

    @include breakpoint(desktop) {
      flex-direction: row;
    }
  }

  .details-container {
    .detail {
      align-items: center;
      display: flex;
      justify-content: flex-start;

      p:first-of-type {
        margin-right: 1rem;
      }
    }

    form {
      .v-input {
        max-width: 75%;

        @include breakpoint(desktop) {
          max-width: 50%;
        }
      }
    }
  }
</style>