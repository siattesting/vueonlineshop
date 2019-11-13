<template>
    <div>
        <h1 class="wrapper">{{ pageTitle }}</h1>
        <ul class="wrapper item-grid">
            <li v-for="product in productsByGender" :key="product.id" class="item-grid__item">
                <router-link :to="{ name: 'product', params: { id: product.id}}">
                    <img class="product-image" :src="makeImagePath(product)" alt="">
                    <p class="product-title">{{ product.name}} </p>
                    <p><em>${{ product.price }}</em></p>
                </router-link>
            </li>
        </ul>
        <!-- RECOMMENDATIONS -->
        <div class="wrapper random-items-wrapper">
        <h2>Our Recommendations</h2>
        <p>Try these on for size!</p>
        <section class="random-items">
          <router-link :to="{ name: 'product', params: { id: randomTop.id}}" class="random-items__item">
            <img class="product-image" :src="makeImagePath(randomTop)" alt="">
            <p class="product-title">{{ randomTop.name }}</p>
            <p><em>${{ randomTop.price }}</em></p>
          </router-link>
          <router-link :to="{ name: 'product', params: { id: randomBottom.id}}" class="random-items__item">
            <img class="product-image" :src="makeImagePath(randomBottom)" alt="">
            <p class="product-title">{{ randomBottom.name }}</p>
            <p><em>${{ randomBottom.price }}</em></p>
          </router-link>
          <router-link :to="{ name: 'product', params: { id: randomFootwear.id}}" class="random-items__item">
            <img class="product-image" :src="makeImagePath(randomFootwear)" alt="">
            <p class="product-title">{{ randomFootwear.name }}</p>
            <p><em>${{ randomFootwear.price }}</em></p>
          </router-link>
        </section>
        <button class="btn btn--grey" @click="recommendRandomOutfit">Shuffle</button>
      </div>
    </div>
</template>

<script>
import { imagePath } from '@/mixins/imagePath.js';
export default {
    name: 'genderOverview',
    mixins: [imagePath],
    created() {
        this.recommendRandomOutfit();
    },
    data () {
        return {
            randomTopId: null,
            randomBottomId: null,
            randomFootwearId: null,
        }
    },
    computed: {
        gender() {
            return this.$route.params.gender;
        },

        pageTitle () {
            // We are making a dynamic page tile that looks at the gender computed property above
            //displays that string in a title case format
            return `${this.gender[0].toUpperCase()}${this.gender.slice(1)}`
            // we are assuming the string is only one word
        },

        productsByGender () {
            return this.$store.getters.productsByGender(this.gender);
            //This particular getter takes the gender computed property as an argument
            //We pass in this.gender so the getter knows by which gender to filter our products
        },

        randomTop() {
            return this.$store.getters.product(this.randomTopId);
        },

        randomBottom() {
            return this.$store.getters.product(this.randomBottomId);
        },

        randomFootwear() {
            return this.$store.getters.product(this.randomFootwearId);
        }
    },

    methods: {
        // https://medium.com/@magyarn/building-an-online-store-with-vue-cli-part-7-8e7c84172e19

        randomProductIdByCategory (category) {
            // => Retrieve a random product within a certain category:
            //Since we are fetching similar information in the same way (a random Id for a given category)
            //we can write one method ad use it for all of them
            let allproductsInCategory = this.productsByGender.filter(p => p.category === category);
            //Generate a randomIndex that is between 0 and however many products there are in allProductInCategory
            let randomIndex = Math.floor(Math.random() * allproductsInCategory.length);
            return allproductsInCategory[randomIndex].id;
        },

        recommendRandomOutfit() {
            this.randomTopId = this.randomProductIdByCategory('Shirts')
            this.randomBottomId = this.randomProductIdByCategory('Pants')
            this.randomFootwearId = this.randomProductIdByCategory('Shoes')
        }
    }
}
</script>

<style lang="scss">
.random-items-wrapper {
  background: #fafafa;
  text-align: center;
  padding: 3rem;
}
.random-items {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.random-items__item {
  flex: 0 0 33%;
}
.item-grid {
  list-style: none;
  padding-left: 0;
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  flex-wrap: wrap;
}
.item-grid__item {
  box-sizing: border-box;
  text-align: center;
  padding: 1rem;
  flex: 0 0 33.3%;
  @media only screen and (max-width: 832px) {
    flex: 0 0 50%;
  }
  @media only screen and (max-width: 475px) {
    flex: 0 0 100%;
  }
}
</style>