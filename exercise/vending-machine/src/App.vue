<template>
  <div id="app">
    <b-container fluid="sm" class="bv-example-row-flex-cols container">
      <b-row>
        <b-col cols="3" align-self="start">
          <b-row class="payment">
            <form ref="form" @submit.stop.prevent="handleSubmit">
              <h2>Please insert your coin(s) here.</h2>
              <h4>Money: {{ totalCoin }} baths</h4>
              <Coin :CoinValue="10" @Insert="coinIncrease" />
              <Coin :CoinValue="5" @Insert="coinIncrease" />
              <Coin :CoinValue="2" @Insert="coinIncrease" />
              <Coin :CoinValue="1" @Insert="coinIncrease" />
              <div>
                <b-button
                  type="submit"
                  variant="success"
                  class="btn-machine"
                  size="lg"
                  v-if="canSubmit"
                >
                  Submit
                </b-button>
                <b-button
                  type="submit"
                  variant="success"
                  class="btn-machine"
                  size="lg"
                  v-else
                  disabled
                >
                  Submit
                </b-button>
                <b-button
                  variant="danger"
                  class="btn-machine"
                  size="lg"
                  @click="refund"
                >
                  refund
                </b-button>
              </div>
            </form>
          </b-row>
          <b-row class="payment">
            <h2>Collect your change here.</h2>
            <b-container class="bv-example-row change-container">
              <b-row class="center-row">
                <b-col sm="3" v-for="a in changeCoins[0]" :key="a">
                  <Coin :CoinValue="10" />
                </b-col>
                <b-col sm="3" v-for="b in changeCoins[1]" :key="b">
                  <Coin :CoinValue="5" />
                </b-col>
                <b-col sm="3" v-for="c in changeCoins[2]" :key="c">
                  <Coin :CoinValue="2" />
                </b-col>
                <b-col sm="3" v-for="d in changeCoins[3]" :key="d">
                  <Coin :CoinValue="1" />
                </b-col>
              </b-row>
            </b-container>
            <b-button
              variant="success"
              size="lg"
              class="reset-change"
              v-if="isReset"
              @click="hasReset"
            >
              reset
            </b-button>
          </b-row>
        </b-col>

        <b-col cols="9" align-self="end">
          <b-container class="bv-example-row">
            <b-row class="center-row">
              <b-col sm="4" v-for="p in products" :key="p.id">
                <Product
                  :coins="totalCoin"
                  :currentProduct="p"
                  :isCanBuy="isSubmit"
                  @buying="getbuying"
                />
              </b-col>
              <b-col sm="4" v-for="p in products" :key="p.name">
                <Product
                  :coins="totalCoin"
                  :currentProduct="p"
                  :isCanBuy="isSubmit"
                  @buying="getbuying"
                />
              </b-col>
            </b-row>
          </b-container>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Product from "./components/Product.vue";
import Coin from "./components/Coin.vue";

export default {
  name: "App",
  components: {
    Product,
    Coin,
  },
  data() {
    return {
      changeCoins: [0, 0, 0, 0],
      products: [],
      qtyProduct: 0,
      totalCoin: 0,
      isSubmit: false,
      canSubmit: false,
      isReset: false,
    };
  },
  methods: {
    coinIncrease(coinparam) {
      if (!this.isSubmit) {
        this.totalCoin += coinparam;
        this.changeCoins = [0, 0, 0, 0];
        this.isReset = false;
        this.canSubmit = true;
      }
    },
    handleSubmit() {
      this.isSubmit = true;
      this.canSubmit = false;
    },
    refund() {
      let tempTotal = this.totalCoin;
      this.totalCoin = 0;
      let typeCoint = [10, 5, 2, 1];
      //coinNum = [10, 5, 2, 1]
      for (let i = 0; i < typeCoint.length; i++) {
        this.changeCoins[i] = Math.floor(tempTotal / typeCoint[i]);
        tempTotal %= typeCoint[i];
      }
      this.isSubmit = false;
      this.canSubmit = false;
      this.isReset = true;
    },
    getbuying(soldProduct) {
      if (soldProduct.in_stock && this.totalCoin >= soldProduct.price) {
        this.totalCoin = this.totalCoin - soldProduct.price;
      }
      this.refund();
    },
    hasReset() {
      this.changeCoins = [0, 0, 0, 0];
      this.isReset = false;
      this.totalCoin = 0;
      this.isSubmit = false;
      this.canSubmit = false;
    },
  },
  mounted: function () {
    fetch("https://www.mocky.io/v2/5c77c5b330000051009d64c9", {
      method: "get",
    })
      .then((response) => response.json())
      .then((jsonData) => {
        this.products = jsonData.data;
        this.qtyProduct = jsonData.total;
      });
    this.changeCoins = [0, 0, 0, 0];
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 3%;
  width: 100%;
}
.btn-machine {
  margin: 1% 2%;
}
.center-row {
  justify-content: center;
}
.shift-right {
  justify-content: flex-end;
}
.change-container {
  margin: unset;
  width: 100%;
  align-self: center;
  border: black solid 1px;
}
.machineScreen {
  width: 20vw;
}
.coinChange {
  width: 20vw;
}
.reset-change {
  margin-top: 2%;
  justify-items: right;
}

.payment {
  padding: 2rem 1rem;
  margin-bottom: 2rem;
  background-color: #e9ecef;
  border-radius: 0.3rem;
}
</style>
