<template>
    <header class="header">
      <h1>Welcome to Bellas Best Burgers</h1>
    </header>
  
    <main>
      <section id="burgers">
        <h2>Choose your burger</h2>
        <p>Explore our menu and select from a variety of mouthwatering options. From classic cheeseburgers to gourmet specialties, there’s something for everyone!</p>
        
        <!-- Wrapper for dynamically generated burgers -->
        <div class="burgerwrapper">
          <Burger
            v-for="burger in burgers"
            :burger="burger"
            :key="burger.name"
            v-on:orderedBurger="addToOrder"
          />
        </div>
      </section>  
      
      <section id="contact">
  <h2>Customer information</h2>
  <p>Fill in your delivery details to ensure your burgers arrive hot and fresh. Quick, easy, and just a few clicks away from deliciousness!</p>
  <form>
    <p>
      <label for="fullname">Full name</label><br>
      <input type="text" id="fullname" v-model="customerName" required placeholder="First- and Last name">
    </p>
    <p>
      <label for="email">E-mail</label><br>
      <input type="email" id="email" v-model="customerEmail" required placeholder="E-mail address">
    </p>
    <p>
      <label for="payment">Payment options</label><br>
      <select id="payment" v-model="paymentOption">
        <option>Credit card</option>
        <option>Klarna</option>
        <option>Swish</option>
        <option>On sight</option>
      </select>
    </p>
    <p>
      <label>Gender</label><br>
      <input type="radio" id="male" value="male" v-model="gender">
      <label for="male">Male</label><br>
      
      <input type="radio" id="female" value="female" v-model="gender">
      <label for="female">Female</label><br>

      <input type="radio" id="nonbinary" value="nonbinary" v-model="gender">
      <label for="nonbinary">Non-Binary</label><br>
      
      <input type="radio" id="undisclosed" value="undisclosed" v-model="gender" checked>
      <label for="undisclosed">Undisclosed</label>
    </p>

    <h3>Please indicate point of delivery</h3>
    <div id="map-container">
      <div id="map" v-on:click="setLocation">
        <div id="dot" :style="{ left: location.x + 'px', top: location.y + 'px' }">T</div>
      </div>
    </div>

    <button type="button" v-on:click="placeOrder">
      <img src="https://cdn-icons-png.freepik.com/256/2435/2435326.png?semt=ais_hybrid" 
        style="width:20px; height:auto;">
      Place Order
    </button>
  </form>
</section>
    </main>
  
    <footer>
      <hr>
      <p>&copy; 2024 Bella's Best Burgers. All rights reserved. Fictional Burgers for your Imagination!</p>
      <p>Follow us on Instagram: @BellasBestBurgers</p>
    </footer>
  
  </template>
  
  <script>
import Burger from '../components/OneBurger.vue';
import menu from '../assets/menu.json'; // Import JSON data
import io from 'socket.io-client';

const socket = io("localhost:3000");

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data() {
    return {
      burgers: menu,
      customerName: '',
      customerEmail: '',
      paymentOption: 'Swish',
      gender: 'undisclosed',
      orderedBurgers: {},
      location: { x: 0, y: 0 }
    };
  },
  methods: {
    getOrderNumber() {
      return Math.floor(Math.random() * 100000);
    },
    addOrder(event) {
      const offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };
      this.location.x = event.clientX - offset.x;
      this.location.y = event.clientY - offset.y;

      console.log('Location:', this.location); // Kontrollera koordinater i konsolen

      socket.emit("addOrder", {
  orderId: "Order " + (Object.keys(this.orderedBurgers).length + 1), // Använd löpande ordernummer
  details: {
    x: this.location.x,
    y: this.location.y,
    customerName: this.customerName,
    customerEmail: this.customerEmail,
    paymentOption: this.paymentOption,
    gender: this.gender,
  },
  orderItems: Object.keys(this.orderedBurgers).map((key) => ({
    name: key,
    amount: this.orderedBurgers[key],
  })),
});
    },
    printOrder() {
      console.log('Customer Name:', this.customerName);
      console.log('Customer Email:', this.customerEmail);
      console.log('Payment Option:', this.paymentOption);
      console.log('Gender:', this.gender);
      console.log('Ordered Burgers:', this.orderedBurgers);
    },
    addToOrder(event) {
      this.orderedBurgers[event.name] = event.amount; // Update order count for burgers
      console.log('Ordered Burgers:', this.orderedBurgers);
    },
    setLocation(event) {
      const offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };
      this.location.x = event.clientX - offset.x;
      this.location.y = event.clientY - offset.y;
      console.log("Updated Location:", this.location); // Kontrollera koordinater
    },
    placeOrder() {
  socket.emit("addOrder", {
    orderId: this.getOrderNumber(), // Unikt ID för beställningen
    details: {
      x: this.location.x,
      y: this.location.y,
      customerName: this.customerName, // Lägg till kundens namn
      customerEmail: this.customerEmail, // Lägg till kundens e-post
      paymentOption: this.paymentOption, // Lägg till betalningsmetod
      gender: this.gender, // Lägg till kundens kön
    },
    orderItems: Object.keys(this.orderedBurgers).map((key) => ({
      name: key,
      amount: this.orderedBurgers[key],
    })), // Namn och mängd av varje burgare
  });

  // Logga för att verifiera att rätt data skickas
  console.log("Order sent with location, items, and customer details:", {
    location: this.location,
    orderItems: this.orderedBurgers,
    customerDetails: {
      name: this.customerName,
      email: this.customerEmail,
      payment: this.paymentOption,
      gender: this.gender,
    },
  });
}
  }
};
</script>
  
  <style>
  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
  body {
    font-family: 'Times New Roman', Times, serif;
    font-size: 12pt;
  }
  
  main, header, footer, nav ul {
    max-width: 60rem;
    margin: 0 auto;
  }
  
  header {
    background-image: url("https://www.splendia.com/wp-content/uploads/2024/03/Plus-beaux-endroits-New-York.jpg");
    background-size: cover;
    background-position: center;
    overflow: hidden;
    width: 100%;
    height: 200px;
    display: flex;
    justify-content: center;
    align-items: center;
    opacity: 0.3;
    margin-bottom: 10px;
  }
  
  header h1 {
    font-family: 'Agbalumo';
    color: white;
    font-size: 3.5rem;
    text-shadow: 6px 6px 12px rgba(0, 0, 0, 0.9);
    text-align: center;
  }
  
  .burgerwrapper {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 20px;
    text-align: left;
  }
  
  #burgers {
    background-color: black;
    color: white;
    border: 2px dashed white;
    padding: 10px;
    margin-bottom: 10px; 
  }
  
  #contact {
    border: 2px dashed black;
    padding: 10px;
  }
  
  .allergen {
    font-weight: bold;
  }
  
  button {
    cursor: pointer;
  }

  #map-container {
  position: relative;
  width: 100%;
  max-width: 1000px; 
  max-height: 600px; 
  overflow: scroll; 
  border: 2px solid black; 
  margin-bottom: 10px;
}

  #map {
  position: relative;
  width: 1920px;
  height: 1078px;
  background: url("/img/polacks.jpg");
  background-size: cover; 
  cursor: pointer; 
  }

  #dot {
  position: absolute;
  width: 20px;
  height: 20px;
  background-color: transparent; /* Gör bakgrunden transparent */
  color: red; /* Färg på "T" */
  font-weight: bold;
  text-align: center;
  pointer-events: none;
}
  </style>