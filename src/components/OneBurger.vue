<template>
   <div class="box">
    <h3>{{ burger.name }}</h3>
    
    <img v-bind:src="burger.img" alt="Burgerpic is missing" title="Burger" style="width: 400px;">
    <ul>
      <section class="ingredients">
        <li v-for="ingredient in burger.ingredients" :key="ingredient">
          {{ ingredient }}</li>
      </section>
      <section class="allergies">
        <li v-for="allergy in burger.allergies" :key="allergy">Contains <span id="glutlac">
          {{ allergy }}</span></li>
      </section>
    </ul>
      Amount:
  
  <button v-on:click="decreaseAmount">-</button>
    {{ amountOrdered }}
  <button v-on:click="increaseAmount">+</button>


   
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
      amountOrdered: 0,
        };
        
  },

  methods: {
    increaseAmount: function () {      // click listener functions
      this.amountOrdered+=1;
      this.$emit('orderedBurgers', { name: this.burger.name, 
                                amount: this.amountOrdered 
                                    }
  );
    },
    decreaseAmount: function () {     // click listener functions
      if (this.amountOrdered > 0) { 
        this.amountOrdered-=1;
        this.$emit('orderedBurgers', { name:   this.burger.name, 
                                amount: this.amountOrdered 
                              }
  );
      }
    }
  }
}
</script>


  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  .allergies {
    color: rgb(250, 80, 80);
 }
 #glutlac{
    font-weight: bold;
 }

 #BURGERS {
    background-color: #282828;
    color: #ffffff;
    border: 5px dashed #ffffff;
    padding:20px;
 }

 .ingredients {
    color: ffffff;
 }
 #BURGERS > div {
    padding: 10px; 
}

.box {
    font-size: 150%;
}
  </style>
  
  