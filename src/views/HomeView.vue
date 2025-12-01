<template>

<header id="headerGroup">
        <img src ="https://thumbs.dreamstime.com/b/wide-facebook-banner-image-piece-delicious-dark-chocolate-cake-covered-fruits-chocolate-topping-black-plate-296415709.jpg" alt="Header image">
        <h1>Welcome to SweetsOnline</h1>
    </header>

    <main>
        <section id="cakes">
            <h2>Select your cake</h2>
            <p>Here you can choose which cake you want to order.</p>

                  <div class="cakes-grid">
                    <Burger
                    v-for="burger in burgers"
                    :burger="burger"
                    :key="burger.name"
                    @orderedBurger="addToOrder($event)"
                  />
                </div>
          </section>


    <section id="contact">
    <form>
        <h2>Customer information</h2>
        <p>Please enter your delivery address and contact information.</p>

        <h3>Delivery information</h3>

        <p>
            <label for="full-name">Full name</label><br>
            <input type="text" 
                   id="full-name" 
                   name="fn" 
                   required placeholder="First and Last name"
                   v-model="customerName">
        </p>

        <p>
            <label for="email">E-mail</label><br>
            <input type="email" 
                   id="email" 
                   name="em" 
                   required placeholder="E-mail address"
                   v-model="customerEmail">
        </p>

        
        <p>Please indicate point of delivery:</p>

        <div id="mapContainer">
          <div id="map" v-on:click="setLocation">

          <div id="dot" :style="{ left: location.x + 'px', top: location.y + 'px' }">
             T
           </div> 
          </div>
        </div>

        <p>
            <label for="payment-method">Payment mnethod</label><br>
            <select id="payment-method" 
                    name="pmm"
                    v-model="cutomerPayment">
                <option>Swish</option>
                <option>Klarna</option>
                <option>Nordea</option>
                <option>Swedbank</option>
                <option>Invoice</option>
            </select>
        </p>

        <h3>Gender</h3>
        <p>
            <input type="radio" id="male" name="gender" value="male" v-model="customerGender">
            <label for="male">Male</label>
        </p>

        <p>
            <input type="radio" id="female" name="gender" value="female" v-model="customerGender">
            <label for="female">Female</label>
        </p>

         <p>
            <input type="radio" id="non-binary" name="gender" value="non-binary" v-model="customerGender">
            <label for="non-binary">Non-binary</label>
        </p>

        <p>
            <input type="radio" id="no-answer" name="gender" value="na" v-model="customerGender">
            <label for="no-answer">Prefer not to say</label>
        </p>

    </form>

</section>


    </main>

    <button type="button" v-on:click="addOrder">
      <img src="https://img.freepik.com/premium-vector/hand-drawn-cake-cake-vector-illustration_99338-166.jpg" 
          alt="Send order" 
          style="width: 40px;">
      Submit Order
    </button>

    <hr> <!-- to seperate the footer from the rest of the website -->
    <footer>
        <!-- You can add footer content here later -->
    </footer>

</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

// constructor for a location
function Location(x, y) {
  this.x = x;
  this.y = y;
}

export default {
  name: 'HomeView',
  components: {
    Burger
  },

  data: function () {
    return {
      burgers: menu,
      orderedBurgers: {}, // burger name -> amount
      location: new Location(0, 0), // click position on map
      nextOrderId: 1, // counter for orders

      customerName: "",
      customerEmail: "",
      customerPayment: "Swish", //if nothing else is chosen, automaticcaly swish is chosen 
      customerGender: "male"
    }
  },

  methods: {
    getOrderNumber: function () {
      return this.nextOrderId++;
    },

    setLocation: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };

      var x = event.clientX - 10 - offset.x;
      var y = event.clientY - 10 - offset.y;

      // save click position in data
      this.location = new Location(x, y);
    },

    addOrder: function () {
      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: {
          x: this.location.x,
          y: this.location.y
        },
        // send ACTUAL burgers + amounts
        orderItems: this.orderedBurgers,  

        // new personal info
        customer: {
          name: this.customerName,
          email: this.customerEmail,
          payment: this.customerPayment,
          gender: this.customerGender
        }
      });
    },


    addToOrder: function (event) {
      // event.name is burger name, event.amount is how many ordered
      this.orderedBurgers[event.name] = event.amount;
      console.log(this.orderedBurgers);
    }
  }
}
</script>




<style>

  #mapContainer {
    width: 1000px;
    height: 500px;
    overflow: scroll;
    border: 8px solid pink;
  }

  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
    background-size: cover;    /* fill the 1920×1078 box */
    background-position: center;  
    cursor: pointer; /* clickable */
    position: relative;
  }

  #dot { /* red dot */
  position: absolute;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: red;
  transform: translate(-50%, -50%);  /* so the dot is centered on the click */

   /* make T appear in the center of the dot  */
  font-size: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
}

  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');

/* ====== Grundinställningar ====== */
body {
    font-size: 12pt;
    font-family:'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}

p {
    color: red;
}

h1 {
    font-family: 'Agbalumo';
    font-size: 36pt;
}

/* Main-innehåll ligger centrerat och får bakgrundsfärg */
main {
    max-width: 80%; 
    margin: 0 auto;
    background-color: bisque;
}

footer {
    width: 100%;
}

/* Fetstil på allergitexten */
.allergies {
    font-weight: bold;
}

/* Sektionen med kakor */
#cakes {
    background-color: lavender;
    color: brown;
}

/* Knapp-styling */
button:hover {
    background-color: lightblue; 
    cursor: pointer;
}

button {
    margin-top: 2rem; /* push button away from content */
}

/* Alla sektioner: marginal, padding och ram */
section {
    margin: 2rem;         
    padding: 1rem;        
    border: 3px dashed currentColor;
}

/* ====== Header med bild och text ovanpå ====== */

#headerGroup {
    position: relative;
    overflow: hidden;
    height: 200px;
    margin: 1rem auto;
    width: 80%;   
}

#headerGroup img {
    width: 100%;
    height: auto;
    opacity: 0.7;
    display: block;
}

#headerGroup h1 {
    position: absolute;
    top: 35%;
    left: 50%;
    transform: translate(-50%, -50%);
    
    color: white;
    font-size: 2.8rem;
    font-weight: bold;
    text-shadow: 0 0 10px black;
}

/* ===== GRID LAYOUT för cakes ===== */

.cakes-grid {
    display: grid;
    grid-template-columns: repeat(3, auto); /* 3 kolumner, bredd utifrån innehåll */
    column-gap: 2rem;                        /* mellanrum horisontellt */
    row-gap: 1rem;                           /* lite mellanrum vertikalt */
    justify-content: center;                 /* CENTRERA hela gridet */
    margin-top: 1.5rem;
}

/* Varje enskild kaka */
.cake {
    text-align: center;       /* rubrik och bild centrerat */
    max-width: 260px;         /* kompakt ruta, mer likt facit */
}

/* Bilder */
.cake img {
    width: 260px;             /* välj en lagom bredd */
    height: auto;
    display: block;
    margin: 0 auto;
}

/* Lista under varje kaka */
.cake ul {
    margin-top: 0.5rem;
    text-align: left;         /* som i facit – vänsterställda punkter */
    padding-left: 1.2rem;
    list-style-position: outside;
}

</style>