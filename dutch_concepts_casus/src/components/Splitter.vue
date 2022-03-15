<template>
    <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <h3>
      Input name:
    </h3>
    <input v-model="input" placeholder="name">
    <button @click="addPerson()">Add</button>
    <h3>
      Input price to split:
    </h3>
    <input type ="number" v-model="price" :min=0 placeholder="price">
    <button @click="setPrice()">Set</button>
    </div>
    <br/>
    <div>
    <table>
      <template v-for="(person, index) in people">
          <tr>
              <th>{{person.name}}</th>
              <th><button @click="subtractDrink(index)">-</button></th>
                <th>{{person.amountOfDrinks}}x</th>
              <th><button @click="addDrink(index)">+</button></th>
                <th><input type="number" v-model="person.formattedCash" v-on:change="event => changeCashManually(event, index)"></th>
          </tr>
      </template>
    </table>
  </div>
  <div v-if="showPaymentMethods">
  <input type="radio" id="ideal" value="ideal" name="payment" v-model="picked">
    <label for="ideal"> Ideal</label>
    <br>
    <input type="radio" id="paypal" value="paypal" name="payment" v-model="picked">
    <label for="paypal"> Paypal</label>
    <br>
    <input type="radio" id="cashapp" value="cashapp" name="payment" v-model="picked">
    <label for="cashapp"> Cash app</label>
    <br>
    <button @click="Pay(picked)">Pay</button>
    <div v-if="amountTooLittle">Please pay the required amount</div>
  </div>
</template>

<style>
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td, th {
  border: 1px solid #dddddd;
  text-align: center;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #dddddd;
}
</style>

<script>
  export default {
    data() {
      return {
        people: [
    
        ],
        input: '',
        price: 0, //price in total, to be filled in by the user
        pricePerBeverage: 0,
        showPaymentMethods: false, //for showing the payment methods, kinda weird to show payment methods without a table with people
        amountTooLittle: false,
      }
    },
    methods: {
        addPerson() {
             if (!this.input) {
            // input value is empty
            return;
    }
            this.people.push({name: this.input, amountOfDrinks: 1, cash: 0, formattedCash: 0})
            this.input = ''
            this.setPrice() //recalculate the price after a person is added
        },
        setPrice() {
             if (this.price == 0 || this.people.length == 0) {
            // input value is empty or people array is empty
            return;
    }
            for(var i = 0; i < this.people.length; i++) {
                this.people[i].cash = this.price / this.people.length
                this.people[i].formattedCash = this.formatNumber(this.price / this.people.length)
                this.people[i].amountOfDrinks = 1
            }

            //the price of the beverage is decided by the price of all drinks divided by the number of people
            //highly improbable in practice, but there needed to be a way to make the + and - buttons work
            this.pricePerBeverage = this.price / this.people.length 
            this.showPaymentMethods = true
        },
        subtractDrink(index) {
            if(this.people[index].amountOfDrinks > 0) {
            this.people[index].amountOfDrinks--;
            this.people[index].cash = this.people[index].amountOfDrinks * this.pricePerBeverage;
            this.people[index].formattedCash = this.formatNumber(this.people[index].amountOfDrinks * this.pricePerBeverage)
            }

        },
        addDrink(index) {
            var money = 0

            for(var i = 0; i < this.people.length; i++) {
                money += this.people[i].cash
            }

            if(money < this.price) {
            this.people[index].amountOfDrinks++;
            this.people[index].cash = this.people[index].amountOfDrinks * this.pricePerBeverage;
            this.people[index].formattedCash = this.formatNumber(this.people[index].amountOfDrinks * this.pricePerBeverage)
            }
        },
        Pay(picked) {
            if (this.price == 0 || this.people.length == 0) {
            // input value is empty or people array is empty
            return;
            }
            var money = 0
            for(var i = 0; i < this.people.length; i++) {
                money += parseFloat(this.people[i].cash)
            }

            console.log(money + ' - ' + this.price)

            if(money == this.price) {
                if(picked == 'ideal'){
                    window.location.href = 'https://www.ideal.com';
                }
                else if(picked == 'paypal'){
                    window.location.href = 'https://www.paypal.com';
                }
                else if(picked == 'cashapp'){
                    window.location.href = 'https://www.cash.app';
                }
            } else {
                this.amountTooLittle = true;
            }
        },
        changeCashManually(event, index) {
            this.people[index].cash = event.target.value
        },
          formatNumber(num) {
            return parseFloat(num).toFixed(2)
        },
    }
  }
</script>

