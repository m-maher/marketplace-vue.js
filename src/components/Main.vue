<template>
    <div id="content">
        <h1>Add Product</h1>
        <form v-on:submit.prevent="createProduct(name, price)">
            <div className="form-group mr-sm-2">
                <input
                    id="productName"
                    type="text"
                    v-model="name"
                    className="form-control"
                    placeholder="Product Name"
                    required />
            </div>
            <div className="form-group mr-sm-2">
                <input
                    id="productPrice"
                    type="text"
                    v-model="price"
                    className="form-control"
                    placeholder="Product Price"
                    required />
            </div>
            <button type="submit" className="btn btn-primary">Add Product</button>
        </form>
        <p></p>
        <h2>Buy Product</h2>
        <table className="table">
            <thead>
                <tr>
                    <th scope="col">#</th>
                    <th scope="col">Name</th>
                    <th scope="col">Price</th>
                    <th scope="col">Owner</th>
                    <th scope="col"></th>
                </tr>
            </thead>
            <tbody id="productList">
                <tr v-bind:key="index" v-for="(product, index) in products">
                    <th scope="row">{{product.id.toString()}}</th>
                    <td>{{product.name}}</td>
                    <td>{{window.web3.utils.fromWei(product.price.toString(), 'Ether')}} Eth</td>
                    <td>{{product.owner}}</td>
                    <td>
                        <button
                            :name="product.id"
                            :value="product.price"
                            v-on:click="purchaseProduct(event.target.name, event.target.value)">  
                            ETH                                     
                        </button>                            
                    </td>
                </tr>                
            </tbody>
        </table>
    </div>
</template>

<script>

export default {
  props: {
    products: Array,
    createProduct: Function,
    purchaseProduct: Function
  },
  data() {
      return {
        productName: '',
        productPrice: '' 
      }
  },
  mount() {
  }
}
</script>

