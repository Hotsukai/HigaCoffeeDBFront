<template>
  <div v-if="coffee" class="card">
    <div class="card-content">
      <p class="title is-4">
        <nuxt-link :to="'/reviews?bean=' + coffee.bean.id">
          {{ coffee.bean.fullName }}
        </nuxt-link>
      </p>
      <p class="subtitle is-6">Coffee-ID : {{ coffee.id }}</p>
      <ul>
        <li>抽出日 : <ConvertTime :time="coffee.createdAt" /></li>
        <li v-if="coffee.dripper">
          <UsersName :users="[coffee.dripper]">Dripper : </UsersName>
        </li>
        <li v-if="coffee.drinkers">
          <UsersName :users="coffee.drinkers">Drinkers : </UsersName>
        </li>
        <li v-if="coffee.memo">
          メモ:
          {{ coffee.memo }}
        </li>
        <CoffeeDetails v-if="showDetails" :coffee="coffee" />
      </ul>
    </div>
    <slot>
      <footer class="card-footer">
        <p v-show="createReview" class="card-footer-item">
          <span>
            <nuxt-link :to="'/reviews/create/' + coffee.id" class="button"
              >レビューを書く</nuxt-link
            >
          </span>
        </p>
        <modal-with-button
          v-show="showReview && coffee.reviewId.length > 0"
          :modal-key="'coffee-' + coffee.id"
          class="card-footer-item"
        >
          <template #open-button> <span> レビューを見る </span></template>
          <template #modal-inner-content>
            <div class="columns">
              <review-card-body
                v-for="(review, index) in coffee.reviews"
                :key="'c-' + coffee.id + 'r-' + index"
                :review="review"
                class="column"
              ></review-card-body>
            </div>
          </template>
        </modal-with-button>
      </footer>
    </slot>
  </div>
</template>



<script lang="ts">
import Vue, { PropType } from "vue";
import ReviewCardBody from "./ReviewCardBody.vue";
import { Coffee, Review } from "~/types/models";
export default Vue.extend({
  components: { ReviewCardBody },
  props: {
    coffee: { type: Object as PropType<Coffee>, default: null },
    showReview: Boolean,
    createReview: Boolean,
    showDetails: { type: Boolean, default: true },
  },
  computed: {
    fullPath: function (): string {
      return "/reviews/create/" + this.coffee.id;
    },
  },
});
</script>

<style lang="scss" scoped>
.card {
  margin-bottom: 1em;
  height: 400px;
  .card-content {
    height: 340px;
    overflow-y: scroll;
  }
  .card-footer-item {
    height: 60px;
  }
}
</style>