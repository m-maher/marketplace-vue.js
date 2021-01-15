<template>
  <div>
    <Navbar v-bind:account="account" />
    <Main v-bind:products="products" v-bind:createProduct="createProduct" v-bind:purchaseProduct="purchaseProduct" />
  </div>
</template>

<script>
import Web3 from "web3";
import Marketplace from "../abis/Marketplace.json";
import Main from "./Main.vue";
import Navbar from "./Navbar.vue";

export default {
  components: {
    Main,
    Navbar
  },
  data() {
    return {
      account: "",
      marketplace: null,
      productCount: 0,
      products: [],
      loading: true
    };
  },
  methods: {
    async loadWeb3() {
      if (window.ethereum) {
        window.web3 = new Web3(window.ethereum);
        await window.ethereum.enable();
      } else if (window.web3) {
        window.web3 = new Web3(window.web3.currentProvider);
      } else {
        window.alert(
          "Non-Ethereum browser detected. You should consider trying MetaMask!"
        );
      }
    },
    async loadBlockchainData() {
      const web3 = window.web3;
      const accounts = await web3.eth.getAccounts();
      console.log(accounts);
      this.account = accounts[0];

      // Network ID
      const networkId = await web3.eth.net.getId();
      const networkData = Marketplace.networks[networkId];
      if (networkData) {
        this.marketplace = web3.eth.Contract(
          Marketplace.abi,
          networkData.address
        );
        this.productCount = await this.marketplace.methods
          .productCount()
          .call();
        // Load Products
        for (var i = 1; i <= this.productCount; i++) {
          const product = await this.marketplace.methods.posts(i).call();
          this.products = [...this.products, product];
        }
        this.loading = false;
      } else {
        window.alert("Marketplace contract not deployed to detected network.");
      }
    },
    createProduct(name, price) {
      this.loading = true;
      this.marketplace.methods
        .createProduct(name, price)
        .send({ from: this.account })
        .once("receipt", receipt => {
          this.loading = false;
          console.log(receipt);
        });
    },
    purchaseProduct(id, price) {
      this.loading = true;
      this.socialNetwork.methods
        .purchaseProduct(id)
        .send({ from: this.account, value: price })
        .once("receipt", receipt => {
          this.loading = false;
          console.log(receipt);
        });
    }
  },
  async mounted() {
    await this.loadWeb3();
    await this.loadBlockchainData();
  }
};
</script>


