<template>
  <div>
    <div>
      <h1>Burgers</h1>
      <Burger v-for="burger in burgers"
              v-bind:burger="burger" 
              v-bind:key="burger.name"/>
    </div>
    <div id="map" v-on:click="addOrder">
      click here
    </div>
  </div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'

const socket = io();

//Object Constructor Function

function MenuItem(pn, pi, kCal, glu, lac) {
    this.productName = pn; // The *this* keyword refers to the object itself
    this.productImage = pi;
    this.calories = kCal;
    this.gluten = glu;
    this.lactose = lac;
    this.productInfo = function() {
        return "name:" + this.productName + ", url:" +this.productImage+ ', kCal:' + this.calories + ", lactose:" + this.lactose;
    };

}

// Objects are then instantiated using the *new* keyword

const burgersArray = [
    new MenuItem('Baconburger', 'https://cdn-bk-se-ordering.azureedge.net/media/oc0hcdpj/bk_kiosk_400x290_singel_baconking.png', '900', true, true),
    new MenuItem('Cheeseburger', 'https://cdn-bk-se-ordering.azureedge.net/media/ywfd4uqs/cheesy-cheese.png', '850', false,false),
    new MenuItem('Chickenburger', 'https://cdn-bk-se-ordering.azureedge.net/media/p0efl3e0/bk_chilicheesecrispychicken_400x290_singel.png', '750', true, false)
];

//for burger in burgersArray 
//new MenuItem = 
console.log( this.productInfo() );

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: burgersArray
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    }
  }
}
</script>

<style>
  #map {
    width: 300px;
    height: 300px;
    background-color: red;
  }
</style>