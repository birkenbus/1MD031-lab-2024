<template>
  <div class="burger">
    <h3>{{ burger.name }}</h3>
    <img :src="burger.img" :alt="'Image of ' + burger.name" :title="burger.name">
    <ul>
      <li>{{ burger.kCal }} kcal</li>
      <li v-if="burger.gluten">Contains <span class="allergen">gluten</span></li>
      <li v-if="burger.lactose">Contains <span class="allergen">lactose</span></li>
    </ul>
    <p>Ordered: {{ amountOrdered }}</p>
    <button @click="increaseOrder">+</button>
    <button @click="decreaseOrder">-</button>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0
    }
  },
  methods: {
    increaseOrder() {
      this.amountOrdered++;
      // Skicka event till föräldern med namnet och nuvarande antal
      this.$emit('orderedBurger', { name: this.burger.name, amount: this.amountOrdered });
    },
    decreaseOrder() {
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
        // Skicka event till föräldern med namnet och nuvarande antal
        this.$emit('orderedBurger', { name: this.burger.name, amount: this.amountOrdered });
      }
    }
  }
}
</script>

<style scoped>
.burger h3 {
  text-align: center;
}

.burger img {
  width: 100%; /* Gör så att bilderna anpassar sig till sin container */
  height: 150px;
  object-fit: cover;
  display: block;
}

.allergen {
  font-weight: bold;
}

.burger li {
  margin: 5px 0; /* Lite avstånd mellan listobjekt */
  font-size: 0.9rem; /* Anpassad textstorlek */
}
</style>