<template>
  <div class="home">
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
    <h1>New Product</h1>
    <div>
      Name:
      <input type="text" v-model="newProductName" />
      Description:
      <input type="text" v-model="newProductDescription" />
      Price:
      <input type="text" v-model="newProductPrice" />
      Image_url:
      <input type="text" v-model="newProductImageUrl" />
      <button v-on:click="createProduct()">Create Product</button>
    </div>

    <div v-for="product in products">
      <h2>Name: {{ product.name }}</h2>
      <button v-on:click="showProduct(product)">Show more info</button>
      <button v-on:click="destroyProduct(product)">Destroy</button>
      <div v-if="product === currentProduct">
        <img v-bind:src="product.image_url" alt />
        <p>Description: {{ product.description }}</p>
        <p>Price: {{ product.price }}</p>
        <div>
          <h4>Edit Product</h4>Name:
          <input type="text" v-model="product.name" />
          Description:
          <input type="text" v-model="product.description" />
          Price:
          <input type="text" v-model="product.price" />
          Image_url:
          <input type="text" v-model="product.image_url" />
          <div>
            <button v-on:click="updateProduct()">Update Product</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
img {
  width: 300px;
}
</style>

<script>
var axios = require("axios");

export default {
  data: function() {
    return {
      message: "Peters Nerdy Stuff Store",
      products: [],
      currentProduct: {},
      errors: [],
      newProductName: "",
      newProductDescription: "",
      newProductPrice: "",
      newProductImageUrl: "",
    };
  },
  created: function() {
    axios.get("/api/products").then(response => {
      this.products = response.data;
    });
  },
  methods: {
    createProduct: function() {
      var params = {
        name: this.newProductName,
        description: this.newProductDescription,
        price: this.newProductPrice,
        image_url: this.newProductImageUrl,
      };
      axios
        .post("/api/recipes", params)
        .then(response => {
          console.log("Success!!!", response.data);
          this.recipes.push(response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showProduct: function(product) {
      if (this.currentProduct === product) {
        this.currentProduct = {};
      } else {
        this.currentProduct = product;
      }
    },
    updateProduct: function(product) {
      var params = {
        name: product.name,
        description: product.description,
        price: product.price,
        image_url: product.image_url,
      };
      axios.patch("/api/products/" + product.id, params).then(response => {
        console.log("Success!!!", response.data);
      });
    },
    destroyProduct: function(product) {
      console.log("Destroy Product...", product.title);
      axios.delete("/api/products/" + product.id).then(response => {
        console.log("Success!!!");
        var index = this.products.indexOf(product);
        this.products.splice(index, 1);
      });
    },
  },
};
</script>