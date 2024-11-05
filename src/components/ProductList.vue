<template>
    <div class="product-box">
      <p v-if="message">
        {{ message }}
      </p>

      
      <h1>Product List</h1>
      <ul>
        <li v-for="product in products" :key="product.id">
          <div>
            <b>{{ product.name }}</b> ${{ product.price }} 
          </div>
         <div>
          <button @click.prevent="getProduct(product._id)">Edit</button>
          <button @click="deleteProduct(product._id)">Delete</button>
         </div>
        </li>
      </ul>
      <div>
       
   
    <form @submit.prevent="addProduct">
      <div class="form">
      <div class="form-input">
        <input type="text" v-model="productName" placeholder="Product Name" required />
      <input type="number" v-model="productPrice" placeholder="Product Price" required />
      </div>
      <button type="submit">Add Product</button>
    </div>
    </form>
   
    
    
    <form v-if="productEditName" @submit.prevent="editProduct(productEditId)">
      
      <div class="form">
       
        <input
        type="text"
        v-model="productEditName"
        placeholder="Product Name"
        required
      />
      <input
        type="number"
        v-model="productEditPrice"
        placeholder="Product Price"
        required
      />
      <button type="submit">Edit Product</button>
      </div>
    </form>
  </div>
     
    </div>
  </template>
  
  <script>
import axios from 'axios';

export default {
  data() {
    return {
      products: [],
      productName: '',
      productPrice: '',
      productEditName: '',
      productEditPrice: '',
      productEditId: null,
      message:'',
    };
  },
  async mounted() {
    await this.fetchProducts();
  },
  methods: {
    async fetchProducts() {
      try {
        const response = await axios.get('http://localhost:3000/products');
        this.products = response.data; // Adjust based on your API response structure
      } catch (err) {
        this.error = 'Failed to fetch products. Please try again later.';
      } finally {
        this.loading = false;
      }
    },
    async addProduct() {
      try {
        // ส่งคำขอ POST ไปยัง API เพื่อเพิ่มสินค้า
        const response = await axios.post('http://localhost:3000/products', {
          name: this.productName,
          price: this.productPrice,
        });
        this.products.push(response.data);

       
        this.productName = '';
        this.productPrice = '';

        this.message = "Add Product Successfully";
       
      } catch (error) {// ถ้ามีข้อผิดพลาด
        console.error('Error adding product:', error);
        alert('Error adding product!');
      }
    },

    async getProduct(id){
      const response = await axios.get('http://localhost:3000/products/'+id);
      this.productEditName = response.data.name;
      this.productEditPrice = response.data.price;
      this.productEditId = response.data._id;
      console.log('Product Name:',response.data.name,'\nProduct Price: ', response.data.price);
      
    },


    async deleteProduct(id){
      const response = await axios.delete('http://localhost:3000/products/'+id);
      this.products = this.products.filter(product => product._id !== id);
      console.log(response);
      this.message = 'Delete Product ID: '+id;
    },

    async editProduct(id) {
  try {
    const updatedProduct = {
      name: this.productEditName,
      price: this.productEditPrice,
    };

    const response = await axios.put(`http://localhost:3000/products/${id}`, updatedProduct);
    console.log('Product updated:', response.data); 
    
    this.products = this.products.map(product => 
      product._id === id ? { ...product, ...response.data } : product
    );

   
    this.message = "Edit Product ID :"+id+" Successfully";
  } catch (error) {
    console.error('Error updating product:', error);
    alert('Failed to update product!');
  }
}


  }


};
</script>


<style>
*{
  margin:0;
  padding:0;
  font-family: sans-serif;
  box-sizing: border-box;
}
body{
  display: flex;
  justify-content: center;
  align-items: center;
  height:100vh;
}
.product-box{
  border-radius: 10px;
  background-color: #fff;
  width:500px;
  box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px;
  padding:30px;
}
li{
  display: flex;
  justify-content: space-between;
  align-items: center;
  
}
h1{
  color: #bda62f;
}
ul{
  margin-top:10px;
  margin-bottom: 10px;
}
button{
  margin:1px;
  background-color: #000;
  color:#fff;
  padding:8px 10px;
  border-radius: 8px;
  border:none;
  cursor:pointer;
}
.form{
  display: flex;
  align-items: center;
 justify-content: space-between;
 border-top:1px solid #ccc;
 padding-top:30px;
 margin-top:30px;
 input{
  margin-right:5px;
  border-radius: 15px;
  padding:6px;
  border:1px solid #ccc;
 }
}

p{
  color:#000;
  font-weight: bold;
  font-size:14px;
  background-color: beige;
  border-radius: 6px;
  padding:10px 15px;
  transform:translate(-20px,-20px);
}
</style>