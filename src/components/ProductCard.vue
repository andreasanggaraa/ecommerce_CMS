<template>
  <div class="uk-card uk-card-hover uk-width-1-5@m">
    <div class="uk-card-header">
        <div class="uk-grid-small uk-flex-middle">
            <div class="uk-width-auto product-image-container">
              <img v-show="!picLoading" @load="picLoaded()" :src="product.image_url" width="150" height="150" :alt="product.name">
              <loading-pic v-show="picLoading"></loading-pic>
            </div>
        </div>
    </div>
    <div class="uk-card-body">
        <h3><b>{{ product.name }}</b></h3>
        <p style="cursor: default;">{{ getRupiah }}</p>
        <p style="cursor: default;">Stock: {{ product.stock }}</p>
    </div>
    <div class="uk-card-footer">
        <div class="footer-nav">
        <router-link class="uk-button uk-button-text btn-update" :to="`/products/edit/${product.id}`">Edit</router-link>
          |
        <button @click.prevent="deleteProduct" class="uk-button uk-button-text btn-delete">Delete</button>
        </div>
    </div>
  </div>
</template>

<script>
import LoadingPic from '../components/LoadingPic'
import UIkit from 'uikit'
// import VueLoadImage from 'vue-load-image'
export default {
  data () {
    return {
      priceRupiah: '',
      picLoading: true
    }
  },
  methods: {
    deleteProduct () {
      this.$store.dispatch('deleteProduct', this.product.id)
        .then(response => {
          this.$emit('fetchProducts')
        })
        .catch(err => {
          console.log(err)
          if (err.response.status === 403) {
            const errors = err.response.data.message
            UIkit.notification({
              message: `${errors}`,
              status: 'danger',
              pos: 'top-right',
              timeout: 1500
            })
          } else {
            UIkit.notification({
              message: 'Product not found! Please refresh page',
              status: 'danger',
              pos: 'bottom-center',
              timeout: 1500
            })
          }
        })
    },
    picLoaded () {
      this.picLoading = false
    }
  },
  props: ['product'],
  components: {
    LoadingPic
  },
  computed: {
    getRupiah () {
      let rupiah = ''
      const pricerev = this.product.price.toString().split('').reverse().join('')
      for (let i = 0; i < pricerev.length; i++) if (i % 3 === 0) rupiah += pricerev.substr(i, 3) + '.'
      return 'Rp. ' + rupiah.split('', rupiah.length - 1).reverse().join('')
    }
  },
  created () {
    this.priceRupiah = this.getRupiah
    this.$store.dispatch('getProducts')
  }
}

</script>

<style scoped>
.uk-card{
  margin: 10px;
  padding: 0;
  border: 1px solid lightgray;
  border-radius: 15px;
  background: rgba(255, 255, 255, 0.75);
}
.uk-card-header{
  height: 35%;
  padding-bottom: 15%;
}
.btn-delete:hover{
  background-color: rgba(255, 15, 15, 0.2);
  border-radius: 5px;
  transform: scale(1.5, 1.5);
}
.btn-update:hover{
  background-color: rgba(15, 135, 255, 0.2);
  border-radius: 5px;
  transform: scale(1.5, 1.5);
}
.product-image-container{
  display: flex;
  justify-content: center;
}
.uk-card-body{
  padding: 25px;
  text-align: center;
  padding-top: 5px;
  padding-bottom: 5px;
}
p{
  margin: 0px;
  padding: 0px;
}
.uk-card-footer{
  padding-bottom: 0;
}
.footer-nav{
  display: flex;
  justify-content: space-around;
}

</style>
