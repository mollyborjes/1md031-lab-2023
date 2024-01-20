<template>


  <header>
        <div class="TOPPAGE">
            <h1>Molly's Börgers</h1>
            <img src="https://broadsword-group.co.uk/wp-content/uploads/2019/11/Fire-header-e1585581283650.jpg" alt="Fire header">
        </div>
    </header>

    <main>
    
        <section id="BURGERS" > 
            
        
            <div>
            <h1>Select burger</h1>
                Please choose your burgers
            </div>

            <div class="burgerWrapper">

            <Burger v-for="burger in burgers"
            v-bind:burger="burger" 
            v-bind:key="burger.name"
            v-on:orderedBurgers="addToOrder($event)"/>
            </div>
        </section>


        <section id="CUSTOMERS"> 
            <h1>Customer Information</h1>
            Please provide necessary information
            <h2>Delivery Information:</h2>
            <div>
                <p>
                    <label for="fullname">Full name</label><br>
                    <input type="text" id="fullname" v-model="fn" required="required" placeholder="First- and Last name">
                </p>
              
                <p>
                    <label for="email">Email</label><br>
                    <input type="email" id="email" v-model="em" required="required" placeholder="E-mail address">
                </p>
          
            </div>

            <div>
                <p>
                    <label for="paymentmethod">Payment </label>
                        <select id="paymentmethod" v-model="pm">

                        <option selected="selected"> Card </option>
                        <option>Cash</option>
                        <option>Swish</option>
                        <option>Klarna</option>
                    </select>
                 </p>
            </div>

      
            <legend>Gender</legend>

            <div>
              <input type="radio" id="female" v-model="gender" value="Female" checked />
              <label for="female">Female</label>
            </div>
          
            <div>
              <input type="radio" id="male" v-model="gender" value="Male" />
              <label for="male">Male</label>
            </div>
            <div>
                <input type="radio" id="Other" v-model="gender" value="Other" />
                <label for="other">Other</label>
              </div>
            <div>
              <input type="radio" id="donotwishtoprovide" v-model="gender" value="Do not wish to provide" />
              <label for="donotwishtoprovide">Do not wish to provide</label>
            </div>
       
        
        </section>
        <section class="mapWrapper">
          Choose the location of the delivery:

        <div id="map" v-on:click="setLocation">

      <div id="dots">
        <div v-bind:style="{position: absolute, left: location.x + 'px', top: location.y + 'px'}" v-bind:key="'dots' + key">
          {{ key }}
        </div>
      </div>
    </div>
      </section>

        <div>
          
            <button type="submit" v-on:click="submit">
            <img src="https://cdn-icons-png.flaticon.com/512/3682/3682321.png" alt="PlaceOrderButton" title="Button" style="width: 10px;">
            Place Order
          </button>
        </div>
    </main>
    <hr>
    <footer>
        Thank you for ordering at Molly's Börgers!
    </footer>


</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

//Object Constructor Function

// Modify your MenuItem object structure

function MenuItem(fn, img, kCal, lac, glut) { 
  this.name = fn;
  this.kCal = kCal
  this.url = img;
  this.lactose = lac;
  this.gluten = glut;
}
new MenuItem() 

export default {
  name: 'HomeView',
  components: {
    Burger
  },
 

  data: function () {
  return {
    orderIDCounter: 0,

    burgers: menu,
    gender: 'Female',
    fn: '',
    em: '',
    pm: 'Card',
    mountOrdered: 0,
    orderedBurgers: [],
    location: { x: 0,
            y: 0
          }
  }
},
computed: {
  customerInfo: function () {
    return {
      fn: this.fn,
      em: this.em,
      pm: this.pm,
      gender: this.gender
    };
    }
  },
  methods: {
    getOrderNumber: function () {
    this.orderIDCounter++;        // order ID ökar med en för varje beställning
    return this.orderIDCounter;
    },
    
    setLocation: function (event) {
      console.log('Click event:', event);
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top};
        this.location = {
          x: event.clientX - 10 -offset.x,
          y: event.clientY - 10 - offset.y
        };
      },
      
      addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      
    },
    
    addToOrder: function (event) {
      const {name, amount} = event;
      const existingBurger = this.orderedBurgers.find(burger => burger.name === name);
      if (existingBurger) {
        existingBurger.amount = amount;
      }
      else {
        this.orderedBurgers.push({name, amount})
      }
      console.log('Ordered Burgers: ', this.orderedBurgers);
    },

    submit: function () {
      console.log('Customer Information:', this.customerInfo);
      console.log('Ordered Burgers:', JSON.parse(JSON.stringify(this.orderedBurgers))); // dispatcher kan inte ta emot objekt vilket orderedBurgers annars var
      
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: {location: this.location,
                                  customerInfo: this.customerInfo
                                },
                                orderItems: this.orderedBurgers
                              }
                 );
    
      this.fn = '';
      this.em = '';
      this.pm = 'Card';
      this.gender = 'Female';
      this.orderedBurgers = [];
      this.location = { x: 0, y: 0 };


    },
    
  }
}
</script>

<style>
  #map {
    background: url('../../public/img/polacks.jpg');
    width: 1920px;
    height: 1078px;
  }

  .mapWrapper {
    height: 600px;
    width: 100%;
    overflow: scroll
}

  body {
    font-family: Futura, sans-serif;
    font-size:16px;
}

@media screen and (max-width: 600px) {
    body {
        font-size: 14px;
    }
    .TOPPAGE h1 {
      width: 100%;
    }
}


.allergies {
    color: rgb(250, 80, 80);
 }
 #glutlac{
    font-weight: bold;
 }
 
 .ingredients {
    color: rgb(255, 255, 255);
 }
 


 #BURGERS {
    background-color: #282828;
    color: #ffffff;
    border: 5px dashed #ffffff;
    padding:20px;
 }
 
 
 button:hover {
    background-color: rgb(92, 203, 255)
 }
 
 #BURGERS {
    margin: 10px 5px;
}
#CUSTOMERS {
    margin: 10px 5px;
    border: 5px dashed #000000;
    padding:20px;

}
#CUSTOMERS h2 {
    font-size: 18px;
}

button {
    margin: 10px 5px;
}

#BURGERS > div {
    padding: 10px; 
}

.TOPPAGE {
    overflow:hidden;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    border-radius: 10px;
    gap:100px

}
.TOPPAGE h1 {
    position: absolute;
    font-size:100px;
    left: 20%;
    margin-top: 120px;
    z-index: 1;
}

.TOPPAGE img {
    width: 100%;
    height: auto;
    opacity:0.8;
    border-radius: 10px;
}
.burgerWrapper {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 100px;
  border-radius: 10px;
}

.dots {
  position: absolute;
}

</style>
