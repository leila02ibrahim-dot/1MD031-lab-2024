<template>
  <div class="cake">
    <h3>{{ burger.name }}</h3>

    <!-- Show how many are ordered -->
    <p>Ordered: {{ amountOrdered }}</p>

    <!-- Buttons to change the amount -->
    <button @click="addBurger">+</button>
    <button @click="removeBurger" :disabled="amountOrdered === 0">-</button>

    <img :src="burger.img" :alt="burger.name">

    <ul>
      <li>{{ burger.price }}</li>
      <li>{{ burger.description }}</li>
      <li>{{ burger.kCal }} kCal</li>

      <li v-if="burger.lactose">
        <span class="allergies">Contains lactose</span>
      </li>
      <li v-else>
        <span class="allergies">Lactose free</span>
      </li>

      <li v-if="burger.gluten">
        <span class="allergies">Contains gluten</span>
      </li>
      <li v-else>
        <span class="allergies">Gluten free</span>
      </li>
    </ul>
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
    addBurger: function () {
      this.amountOrdered++;
      this.$emit('orderedBurger', {
        name: this.burger.name,
        amount: this.amountOrdered
      });
    },
    removeBurger: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
        this.$emit('orderedBurger', {
          name: this.burger.name,
          amount: this.amountOrdered
        });
      }
    }
  }
}
</script>

<style scoped>
.cake {
  text-align: center;
  max-width: 260px;
}

.cake img {
  width: 260px;
  height: auto;
  display: block;
  margin: 0 auto;
}

.cake ul {
  margin-top: 0.5rem;
  text-align: left;
  padding-left: 1.2rem;
  list-style-position: outside;
}

.allergies {
  font-weight: bold;
}
</style>
