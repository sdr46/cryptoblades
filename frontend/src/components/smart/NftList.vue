import { Nft } from '@/views/Market.vue';
import { Nft } from '@/views/Market.vue';
<template>
  <div>
    <div class="centered-text-div" v-if="isShop && (!nfts || nfts.length === 0)">
      <span>Nothing to buy at this time</span>
    </div>
    <ul v-if="isShop" class="nft-grid">
      <li class="nft" v-for="nft in nfts" :key="nft.nftType + nft.nftId"
      v-tooltip.top="{ content: itemDescriptionHtml(nft) , trigger: (isMobile() ? 'click' : 'hover') }"
                      @mouseover="hover = !isMobile() || true"
                      @mouseleave="hover = !isMobile()"
                      >
        <nft-icon :nft="nft" :isShop="isShop"/>

        <b-button
          variant="primary"
          class="shop-button"
          @click="buyItem(nft)">
          <span class="gtag-link-others" tagname="forge_weapon">
            Buy ({{ nft.nftPrice }} SKILL)
          </span>
        </b-button>
      </li>
    </ul>

    <ul v-if="!isShop" class="nft-grid">
      <li class="nft" v-for="nft in nonIgnoredNfts" :key="nft.nftType + nft.nftId">
        <nft-icon :nft="nft" :isShop="isShop"/>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { mapActions, mapGetters } from 'vuex';
import {Nft as nftItem} from '../../interfaces/Nft';
import { SkillShopListing } from '../../interfaces/SkillShopListing';
import NftIcon from '../NftIcon.vue';

const sorts = [
  { name: 'Any', dir: '' },
  { name: 'Price: Low -> High', dir: 1 },
  { name: 'Price: High -> Low', dir: -1 },
];

export default {
  props: ['nfts','showGivenNfts','isShop'],

  data() {
    return {
      starFilter: '',
      elementFilter: '',
      favorites: {},
      priceSort: '',
      sorts,
      showFavoriteNfts: true
    };
  },

  components: {
    NftIcon
  },

  computed: {
    ...mapGetters(['nftsWithIdType']),

    nftsToDisplay() {
      return this.nfts;
    },

    displayNfts() {
      return this.nftsWithIdType(this.nftsToDisplay);
    },

    nonIgnoredNfts() {
      return this.displayNfts;
    }
  },

  methods: {
    ...mapActions(['purchaseShield', 'purchaseRenameTag', 'purchaseWeaponRenameTag']),
    async onShieldBuy() {
      await this.purchaseShield();
    },
    async buyItem(item: nftItem) {
      if(item.nftType === 'shield'){
        await this.purchaseShield();
      }

      if(item.nftType === 'CharacterRenameTag'){
        await this.purchaseRenameTag();
      }

      if(item.nftType === 'WeaponRenameTag'){
        await this.purchaseWeaponRenameTag();
      }
    },
    itemDescriptionHtml(item: SkillShopListing): string {
      return item.name + '<br>' + item.description;
    }
  }
};
</script>

<style>
.nft-grid {
  list-style-type: none;
  justify-content: center;
  margin: 0;
  display: grid;
  padding: 0.5em;
  grid-template-columns: repeat(auto-fit, 12em);
  gap: 0.5em;
}
.nft {
  width: 12em;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 5px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.centered-text-div {
  text-align: center;
}

.shop-button {
  position: relative;
  width: 12rem;
}
</style>