
<template>
  <div class="burger-item">
    <h3>{{ burger.name }}</h3>

    <img :src="burger.url" :alt="burger.name" width="250">

    <h4>Information</h4>

    <ul>
      <li>Calories: {{ burger.kCal }} kcal</li>
      <li>Gluten: {{ burger.gluten ? 'Yes' : 'No' }}</li>
      <li>Lactose: {{ burger.lactose ? 'Yes' : 'No' }}</li>
    </ul>
    <p>Amount ordered: {{ amountOrdered }}</p>

    <button v-on:click="changeAmount(1)">+</button>
    <button v-on:click="changeAmount(-1)">-</button>
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
    changeAmount: function (change) {
      this.amountOrdered = Math.max(0, this.amountOrdered + change);
      this.$emit('update-amount', this.amountOrdered);
    }
  }
}
</script>

<style scoped>
.burger-item {
  margin: 0;
  padding: 10px;
}

.burger-item img {
  width: 100%;
  height: 250px;
  object-fit: cover;
}
</style>
