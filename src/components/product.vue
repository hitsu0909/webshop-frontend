<template>
<div v-for="p in products" :key="p.product_id">
  <h3>{{ p.product_name }}</h3>
  <p>{{ p.price }}円</p>
  <img :src="p.image_path" width="100">
  </div>
</template>

<script>
import { onMounted, ref } from 'vue'

export default {
  setup() {
    const products = ref([])

    onMounted(async () => {
      const res = await fetch('https://your-backend.onrender.com/products')
      const data = await res.json()
      products.value = data
    })

    return { products }
  }
}

</script>