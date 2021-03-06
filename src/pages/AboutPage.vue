<template>
<div class="about-page grid-container full">
  <header-section showNavSearch="true"></header-section>
  <div class="about-page_body">
    <h1>About CC Search</h1>
      <p class="about-page_lead-paragraph">
        There is no larger compendium of shared human knowledge and creativity than the Commons,
         including over 1.4 billion digital works available under CC tools. Despite the tremendous
         growth of the Commons, and the widespread use of the CC licenses and public domain marks,
         there is no simple way to maximize use of, and engagement with, all of that content. There
         is no front door — no tool designed for the general public to facilitate discovery for the
         purpose of reuse and remix, to simplify the license terms, make attribution easy, or
         support curation, and crowdsourced metadata.
      </p>
      <p>
        Creative Commons’ “CC Search” project will develop and release an open online search and
        re-use tool that will allow high-quality content from the commons to surface in a more
        seamless and accessible way. Our beta relies on open APIs and the Common Crawl dataset
        and focuses on photos as its first media type. It is meant to elicit discussion and inform
        our development as we build out the full set of tools. “CC Search” will enable users to
        curate, tag, and remix that content. It will go beyond simple search to aggregate results
        from across the hundreds of public repositories into a single catalog, and also facilitate
        the use and re-use through tools like curated lists, saved searches, one- or no-click
        attribution, and provenance.
      </p>
      <h2>New Release</h2>
      <p>
        This release contains several new features, including AI image tags generated from
        our collaborator, Clarifai. Clarifai is a best in class image classification software
        that provides tagging support and visual recognition. Clarifai’s API was integrated
        in the process-flow as a means to automatically generate tags for the new and existing
        images. This means that CC search has machine-generated tags, user-defined tags, and
        platform-defined tags that were obtained from the web crawl data. Collectively,
        these will enhance the user’s search experience and improve the quality of the results.
        Currently, 10.3 million images have their respective Clarifai tags and the outstanding
        images will be integrated on an ongoing basis. Tags generated via Clarifai are marked with
        a <img class="photo_tag-provider-badge" src="@/assets/clarifai_logo.png"> on the detail page
        for each image. With this addition, we’re not just cataloging the commons, we’re making it
        better. Thank you to Clarifai for their support.
      </p>
      <h2>Providers</h2>
      <div class="about-page_provider-stats-ctr">
        <table class="about-page_provider-stats-table">
          <thead>
            <th>Provider</th>
            <th>Domain</th>
            <th># CC Licensed Works</th>
          </thead>
          <tbody>
            <tr v-for="(imageStat, index) in imageStats"
                v-if="getProviderName(imageStat.provider_name)"
                :key="index">
              <td>{{ getProviderName(imageStat.provider_name) }}</td>
              <td>
                <a :href="getProviderURL(imageStat.provider_name)">
                  {{ getProviderURL(imageStat.provider_name) }}
                </a>
              </td>
              <td>{{ getProviderImageCount(imageStat.image_count) }}</td>
            </tr>
          </tbody>
        </table>
      </div>
  </div>
  <footer-section></footer-section>
</div>
</template>

<script>
import HeaderSection from '@/components/HeaderSection';
import FooterSection from '@/components/FooterSection';
import { FETCH_IMAGE_STATS } from '@/store/action-types';
import ImageProviderService from '@/api/ImageProviderService';

const AboutPage = {
  name: 'about-page',
  components: {
    HeaderSection,
    FooterSection,
  },
  computed: {
    imageStats() {
      return this.$store.state.imageStats.sort((a, b) => {
        const nameA = a.provider_name.toUpperCase();
        const nameB = b.provider_name.toUpperCase();

        if (nameA < nameB) {
          return -1;
        }

        if (nameA > nameB) {
          return 1;
        }

        return 0;
      });
    },
  },
  methods: {
    getProviderName(providerName) {
      const provider = ImageProviderService.getProviderInfo(providerName);

      return provider && provider.name;
    },
    getProviderURL(providerName) {
      const provider = ImageProviderService.getProviderInfo(providerName);

      return provider && provider.url;
    },
    getProviderImageCount(imageCount) {
      return (imageCount).toLocaleString('en');
    },
    getSortedStats(imageStats) {
      return imageStats.sort((a, b) => a.name > b.name);
    },
  },
  beforeMount() {
    this.$store.dispatch(FETCH_IMAGE_STATS);
  },
};

export default AboutPage;
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  h1 {
    margin-bottom: .44117647em;
    font-size: 2.125em;
    font-weight: normal;
    letter-spacing: initial;
    line-height: 1.25;
    text-transform: initial;
  }

  h2 {
    margin-bottom: .57692308em;
    font-size: 1.5em;
    font-weight: normal;
    letter-spacing: initial;
    line-height: 1.25;
    text-transform: initial;
  }

  .about-page_body {
    font-size: 1em;
    margin: 45px !important;
  }

  .about-page_lead-paragraph {
    margin-bottom: 1em;
  }

  .photo_tag-provider-badge {
    height: 24px;
  }

  /* Small only */
  @media screen and (max-width: 39.9375em) {
    .about-page_body {
      margin: 30px 15px 15px 15px !important;
    }
  }
</style>
