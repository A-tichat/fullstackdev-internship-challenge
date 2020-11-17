<template>
  <div class="box-contranier">
    <b-jumbotron>
      <h3>{{ currentProduct.name }}</h3>
      <b-img
        thumbnail
        fluid
        :src="currentProduct.image"
        :alt="currentProduct.name"
        rounded="circle"
        style="width: 8vw; height: 14vh; margin-bottom: 2%"
      ></b-img>
      <h5>Price: {{ currentProduct.price }}</h5>
      <div>
        <b-button
          type="submit"
          id="show-btn"
          @click="modalShow = !modalShow"
          variant="success"
          href="#"
          v-if="isCanBuy"
          size="lg"
        >
          Buy
        </b-button>
        <b-button variant="success" href="#" v-else size="lg" disabled>
          Buy
        </b-button>
        <b-modal
          id="modal-submit-product"
          v-model="modalShow"
          centered
          :title="currentProduct.name"
          @ok="confirmProduct"
          ok-only
        >
          <p v-if="!currentProduct.in_stock">
            Sorry, this product out of stock!
          </p>
          <p v-else-if="coins < currentProduct.price">
            Sorry, Don't have enough coins!
          </p>
          <p v-else>Thank you!</p>
        </b-modal>
      </div>
    </b-jumbotron>
  </div>
</template>


<script>
export default {
  name: "Product",
  props: {
    coins: Number,
    currentProduct: Object,
    isCanBuy: Boolean,
  },
  data() {
    return {
      modalShow: false,
    };
  },
  methods: {
    confirmProduct() {
      this.$emit("buying", this.currentProduct);
    },
  },
};
</script>
