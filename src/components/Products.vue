<template>
  <div class="products">
    <h3>Links</h3>
    <div class="card">
      <div class="card-header">
        Add o link
      </div>
      <div class="card-body">
        <form class="form-inline" v-on:submit.prevent="onSubmit">
          <div class="form-group">
            <label>Link</label>
            <input v-model="productData.product_name" type="text" class="form-control ml-sm-2 mr-sm-4 my-2" required>
          </div>
          <div class="ml-auto text-right">
            <button type="submit" class="btn btn-primary my-2">Add</button>

          </div>
        </form>
      </div>
    </div>

    <div class="card mt-5">
      <div class="card-header">
        Lista de Link
      </div>
      <div class="card-body">
        <div class="table-responsive">
          <table class="table">
            <thead>
              <tr>
                <th>
                  Link
                </th>
                <th>
                  Ação
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="product in sortedProducts" v-bind:key="product.id">
                <template v-if="editId == product.id">
                  <td><input v-model="editProductData.product_name" type="text"></td>
                  <td>
                    <span class="icon">
                      <i  @click="onEditSubmit(product.id)" class="fa fa-check"></i>
                    </span>
                    <span class="icon">
                      <i  @click="onCancel" class="fa fa-ban"></i>
                    </span>
                  </td>
                </template>
                <template v-else>
                  <td>
                    {{product.product_name}}
                  </td>
                  <td>

                    <a href="#" class="icon">
                      <i v-on:click="onDelete(product.id)" class="fa fa-trash"></i>
                    </a>
                    <a href="#" class="icon">
                      <i v-on:click="onEdit(product)" class="fa fa-pencil"></i>
                    </a>
                    <router-link
                    :to="{
                      name:'ProductPage',
                      params:{id: product.id}
                    }"
                    class="icon"
                    >
                    </router-link>
                  </td>
                </template>
              </tr>

            </tbody>
          </table>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import db from '@/db'
export default {
  name: 'Products',
  data () {
    return {
      editId: '',
      productData: {
        'id' : '',
        'product_name': '',
      },
      editProductData: {
        'id' : '',
        'product_name': '',
      },
      products: []
    }
  },
  created() {
    this.getProducts()
  },
  computed:{
    sortedProducts(){
      return this.products.slice().sort((a,b)=>{
        return a.product_id - b.product_id
      })
    }
  },
  methods: {
    getProducts() {
      db.collection('products').get().then(querySnapshot =>{
        const products = []
        // querySnapshot.forEach((doc)=>{
        //   products.push(doc.data())
        // })
        const productsArray = []
        let i = 0
        querySnapshot.forEach((doc)=>{
          productsArray.push(doc.data())
          productsArray[i].id = doc.id
          products.push(productsArray[i])
          i++
        })
        // for(let key in querySnapshot.docs){
        //   productsArray.push(querySnapshot.docs[key].data())
        //   productsArray[key].id = querySnapshot.docs[key].id
        //   products.push(productsArray[key])
        // }
        this.products = products
      })
    },
    onSubmit(){
      db.collection('products').add(this.productData).then(this.getProducts)
      this.productData.product_name = ''

    },
    // onDelete(product_id){
    //   db.collection('products').where('product_id', '==', product_id).get().then(querySnapshot =>{
    //     querySnapshot.forEach(doc=>{
    //       doc.ref.delete().then(this.getProducts)
    //     })
    //   })
    // }
    onDelete(id){
      db.collection('products').doc(id).delete().then((data)=> {
          this.getProducts()
      })
    },
    onEdit(product){
      this.editId = product.id
      this.editProductData.product_name = product.product_name
    },
    onCancel(){
      this.editId = ''
      this.editProductData.product_name = ''
    },
    onEditSubmit (id){
      db.collection("products").doc(id).set(this.editProductData).then(
        this.getProducts)
        this.editId = ''
        this.editProductData.product_name = ''
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3{
  text-align: center;
  margin-top: 30px;
  margin-bottom: 20px;
}
.icon{
  margin-right: 10px;
}
.icon i{
  cursor: pointer;
}
</style>
