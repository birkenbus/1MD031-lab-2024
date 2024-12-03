<template>
  <div id="orders">
    <div id="orderList">
      <div v-for="(order, key) in orders" :key="'order'+key">
        <strong>{{ order.orderId }}:</strong>
        <ul>
          <li v-for="(item, index) in order.orderItems" :key="'item' + index">
            {{ item.name }} x {{ item.amount }}
          </li>
        </ul>
        <p>
          <em>
            {{ order.details.customerName }} ({{ order.details.customerEmail }},
            {{ order.details.paymentOption }}, {{ order.details.gender }})
          </em>
        </p>
      </div>
      <button v-on:click="clearQueue">Clear Queue</button>
    </div>
    <div id="dots">
      <div
        v-for="(order, key) in orders"
        :style="{ left: order.details.x + 'px', top: order.details.y + 'px' }"
        :key="'dots' + key"
      >
        {{ order.orderId }}
      </div>
    </div>
  </div>
</template>
<script>
import io from "socket.io-client";
const socket = io("localhost:3000");

export default {
  name: "DispatcherView",
  data: function () {
    return {
      orders: null, // Inkommande beställningar
    };
  },
  created: function () {
    socket.on("currentQueue", (data) => (this.orders = data.orders));
  },
  methods: {
    clearQueue: function () {
      socket.emit("clearQueue");
    },
    changeStatus: function (orderId) {
      socket.emit("changeStatus", { orderId: orderId, status: "Annan status" });
    },
  },
};
</script>

<style>
#orderList {
  top: 1em;
  left: 1em;
  position: absolute;
  z-index: 2;
  color: black;
  background: rgba(255, 255, 255, 0.5);
  padding: 1em;
}

#dots {
  position: relative;
  margin: 0;
  padding: 0;
  background-repeat: no-repeat;
  width: 1920px;
  height: 1078px;
  cursor: crosshair;
  background-image: url("/img/polacks.jpg");
}

#dots div {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width: 20px;
  height: 20px;
  text-align: center;
}
</style>