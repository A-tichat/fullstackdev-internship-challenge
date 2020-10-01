<template>
  <div id="app">
    <div id="left">
      <b-row class="shift-right">
        <b-jumbotron class="fix-width">
          <form ref="form" @submit.stop.prevent="handleSubmit">
            <h1>Please insert your coin(s) here.</h1>
            <h2>Money: {{ totalCoin }} baths</h2>
            <b-button
              pill
              variant="warning"
              class="coin-icon"
              @click="coinIncrease(10)"
            >
              10
            </b-button>
            <b-button
              pill
              variant="warning"
              class="coin-icon"
              @click="coinIncrease(5)"
            >
              5
            </b-button>
            <b-button
              pill
              variant="warning"
              class="coin-icon"
              @click="coinIncrease(2)"
            >
              2
            </b-button>
            <b-button
              pill
              variant="warning"
              class="coin-icon"
              @click="coinIncrease(1)"
            >
              1
            </b-button>
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
        </b-jumbotron>
      </b-row>

      <b-row class="shift-right">
        <b-jumbotron class="fix-width">
          <h1>Collect your change here.</h1>
          <b-container class="bv-example-row change-container">
            <b-row class="center-row">
              <b-col sm="3" v-for="a in changeCoins[0]" :key="a">
                <b-button pill variant="warning" class="coin-icon">
                  10
                </b-button>
              </b-col>
              <b-col sm="3" v-for="b in changeCoins[1]" :key="b">
                <b-button pill variant="warning" class="coin-icon">
                  5
                </b-button>
              </b-col>
              <b-col sm="3" v-for="c in changeCoins[2]" :key="c">
                <b-button pill variant="warning" class="coin-icon">
                  2
                </b-button>
              </b-col>
              <b-col sm="3" v-for="d in changeCoins[3]" :key="d">
                <b-button pill variant="warning" class="coin-icon">
                  1
                </b-button>
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
        </b-jumbotron>
      </b-row>
    </div>
    <div id="right">
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
        </b-row>
      </b-container>
    </div>
  </div>
</template>

<script>
import Product from "./components/Product.vue";

export default {
  name: "App",
  components: {
    Product,
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
  margin-top: 30px;
}
#left {
  float: left;
  width: 35%;
}
#right {
  float: right;
  width: 65%;
}
.coin-icon {
  font-size: 3em;
  width: 90px;
  height: 90px;
  margin: 10px 10px;
}
.btn-machine {
  margin: 10px 20px;
}
.center-row {
  justify-content: center;
}
.shift-right {
  justify-content: flex-end;
}
.change-container {
  border: solid 2px black;
}
.fix-width {
  width: 90%;
}
.reset-change {
  margin-top: 20px;
  justify-items: right;
}
</style>
