<template>
  <b-button
    pill
    variant="warning"
    class="coin-icon"
    @click="clicked(CoinValue)"
  >
    {{ CoinValue }}
  </b-button>
</template>

<script>
export default {
  name: "Coin",
  props: {
    CoinValue: Number,
  },
  methods: {
    clicked() {
      this.$emit("Insert", this.CoinValue);
    },
  },
};
</script>

<style>
.coin-icon {
  font-size: 1.3rem;
  width: 18%;
  height: 20%;
  margin: 1%;
}
</style>