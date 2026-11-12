
<template>
  <div>
    <header>
      <h1>Välkommen till Unlonely Burger</h1>
      <img
        src="https://images.unsplash.com/photo-1448375240586-882707db888b?w=800"
        alt="header image"
      >
    </header>

    <main>
      <section id="burgers">
        <h2>Select Your Burgers</h2>
        <p>Section for selecting cheeseburgers</p>

        <div class="burger-grid">
          <Burger
           v-for="burger in burgers"
           :key="burger.name"
           :burger="burger"
           @update-amount="orderedBurgers[burger.name] = $event"
         />
        </div>
      </section>

      <section id="delivery-map">
        <h2>Select Your Delivery Location</h2>
        <p>Click on the map to choose your delivery location.</p>

        <div class="map-container">
        <div id="map" v-on:click="setLocation">
        <span
          class="target"
          v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }"
          >T</span>
        </div>
       </div>
      </section>

      <section id="customer">
        <h2>Customer Information</h2>
        <p>This is where you provide customer information</p>
        <h3>Delivery information:</h3>

        <form id="order-form">
          <p>
            <label for="name">Full Name</label>
            <br>
            <input type="text" id="name" v-model="customerName"
                   placeholder="First and Last Name">
          </p>

          <p>
            <label for="email">Email</label>
            <br>
            <input type="email" id="email" v-model="email"
                   placeholder="E-mail address">
          </p>

          <p>Gender</p>

          <input type="radio" id="male" v-model="gender" value="male">
          <label for="male">Male</label>

          <input type="radio" id="female" v-model="gender" value="female">
          <label for="female">Female</label>

          <input type="radio" id="not-specified" v-model="gender"
                 value="not-specified" checked>
          <label for="not-specified">Don't wish to provide</label>

          <p>
            <label for="payment">Preferred payment method</label>
            <br>
            <select id="payment" v-model="payment">
              <option selected>Swish</option>
              <option>Card</option>
              <option>Paypal</option>
              <option>Klarna</option>
            </select>
          </p>
        </form>
      </section>

      <button type="button" v-on:click="submitOrder">
        <img
          src="https://img.icons8.com/color/48/checkmark--v1.png"
          alt=""
          width="24"
        >
        Submit Order
      </button>
    </main>

    <hr>

    <footer>
      ©2026 Unlonely Burgers Inc
    </footer>
  </div>
</template>


<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, url, kCal, gluten, lactose) {
    this.name = name;
    this.url = url;
    this.kCal = kCal;
    this.gluten = gluten;
    this.lactose = lactose;
}

const menuItems = [
    new MenuItem("Mayoburger", "https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=400", 450, true, true),
    new MenuItem("Avocado Burger", "https://images.unsplash.com/photo-1550547660-d9450f859349?w=400", 400, true, false),
    new MenuItem("Lonely Cheeseburger", "https://images.unsplash.com/photo-1571091718767-18b5b1457add?w=400", 350, false, false)
];

console.log(menuItems);
export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
       burgers: menu,
       orderedBurgers: {},
       location: { x: 0, y: 0 },
       customerName: '',
       email: '',
       gender: 'not-specified',
       payment: 'Swish'
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    setLocation: function (event) {
      var offset = event.currentTarget.getBoundingClientRect();

      this.location = {
      x: event.clientX - offset.left,
      y: event.clientY - offset.top
    };
  },

    submitOrder: function () {
      console.log("Name:", this.customerName);
      console.log("Email:", this.email);
      console.log("Gender:", this.gender);
      console.log("Payment:", this.payment);
      console.log("Ordered Burgers:", this.orderedBurgers);
      console.log("Delivery Location:", this.location);

    socket.emit("addOrder", {
      orderId: this.getOrderNumber(),
      details: {
       x: this.location.x,
       y: this.location.y,
       name: this.customerName,
       email: this.email,
       gender: this.gender,
       payment: this.payment
       },
      orderItems: Object.fromEntries(
          Object.entries(this.orderedBurgers)
          .filter(([name, amount]) => amount > 0)
        )
     });
   }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap');
body {
    font-family: 'Poppins', sans-serif;
    font-size: 12pt;
}

p {
    color: red;
}
main, header, footer, nav ul {
    max-width: 40rem;
    margin: 0 auto 0 auto;
}
main {
    background-color: bisque;
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */

header {
    position : relative;
    width: auto;
    height: 250px;
    overflow: hidden;
    margin: 20px auto;
}
header img {
    display: block;
    width: 100%;
    height: 250px;
    object-fit: cover;
    object-position: center;
    opacity: 0.6;
}
header h1 {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    margin: 0;
    padding: 60px 0;
    text-align: center;
    font-size: 28pt;
    z-index: 1;
}
nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: grey;
    padding: 1em;
}

.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: bold;
}
.constituents {
    font-weight: bold;
}
#burgers {
    background-color: black;
    color: white;
}
#burgers .burger-grid > div {
    margin: 0;
    padding: 10px;
}
button {
    margin-top: 20px;
    margin-bottom: 20px;
}
button:hover {
    background-color: lightgreen;
    cursor: pointer;
}
section {
    margin: 20px;
    padding: 25px;
    border: 2px dashed currentColor;
}
.burger-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 20px;
}
.burger-grid img {
    width: 100%;
    height: 250px;
    object-fit: cover;
}
.map-container {
  width: 100%;
  height: 400px;
  overflow: auto;
}

#map {
  position: relative;
  width: 1920px;
  height: 1078px;
  background-image: url('/img/polacks.jpg');
  background-repeat: no-repeat;
  cursor: crosshair;
}

.target {
  position: absolute;
  color: red;
  font-size: 24px;
  font-weight: bold;
  pointer-events: none;
}
</style>