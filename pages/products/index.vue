<template>
  <div>

    <div class="flex flex-wrap justify-center gap-8 bg-white">
      <Produit v-for="skin in displayedWeaponSkins" :key="skin.uuid" :title="skin.displayName"
        :image="getFullRender(skin)" :contentTier="getcontenttier(skin)" />
    </div>

    <!-- Pagination -->

    <div class="pagination">
      <button v-for="pageNumber in totalPages" :key="pageNumber" @click="changePage(pageNumber)">{{ pageNumber
        }}</button>
    </div>

  </div>
</template>

<script>
import Produit from '~/components/produit.vue'

export default {
  data() {
    return {
      weaponSkins: [],
      currentPage: 1,
      productsPerPage: 24 // Modifier si nécessaire RGAPI-ff2b639c-c610-4366-ba88-365d2f3b3522
    }
  },
  async fetch() {
    await this.fetchWeaponSkins();
  },
  methods: {
    async fetchWeaponSkins() {
      try {
        const response = await this.$axios.$get('https://valorant-api.com/v1/weapons/skins', {
          params: {
            page: this.currentPage,
            perPage: this.productsPerPage
          }
        })
        this.weaponSkins = response.data
      } catch (error) {
        console.error(error)
      }
    },
    async getcontenttier(skin) {
      const contentUuid = skin.contentTierUuid;
      try {
        const response = await this.$axios.$get(`https://valorant-api.com/v1/contenttiers/${contentUuid}`);
        this.tiertab = response;
        return this.tiertab.data.displayName;
      } catch (error) {
        console.error(error);
      }
    },
    getFullRender(skin) {
      if (skin.chromas && skin.chromas.length > 0) {
        return skin.chromas[0].fullRender
      } else {
        return null
      }
    },
    changePage(pageNumber) {
      this.currentPage = pageNumber;
      this.fetchWeaponSkins();
    }
  },
  computed: {
    displayedWeaponSkins() {
      const startIndex = (this.currentPage - 1) * this.productsPerPage;
      const endIndex = startIndex + this.productsPerPage;
      return this.weaponSkins.slice(startIndex, endIndex);
    },
    totalPages() {
      return Math.ceil(this.weaponSkins.length / this.productsPerPage);
    }
  },
  components: {
    Produit
  }
}
</script>