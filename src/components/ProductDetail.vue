<template>
  <div class="container">
    <HeaderBar />
    <main class="main-content">

      <div v-if="product">
        <h2 class="product-detail-title">
          {{ product.name }}
        </h2>

        
<!-- ✅ 上段 -->
      <div class="product-detail-container">

        <!-- 左 -->
        <div class="product-detail-image">
          <img :src="product.image" width="200">
        </div>

        <!-- 右 -->
        <div class="product-detail-info">

          <h3 class="detail-section-title">■商品情報</h3>

          <table class="product-info-table">
            <tbody>

              <tr>
                <td class="info-label">商品番号:</td>
                <td class="info-value">{{ product.display_id }}</td>
              </tr>

              <tr>
                <td class="info-label">メーカー:</td>
                <td class="info-value">{{ product.maker }}</td>
              </tr>

              <tr>
                <td class="info-label">カテゴリ:</td>
                <td class="info-value">{{ product.category }}</td>
              </tr>

              <tr>
                <td class="info-label">価格：</td>
                <td class="info-value">
                  {{ formatPrice(product.price) }}
                </td>
              </tr>

              <tr>
                <td class="info-label">評価:</td>
                <td class="info-value">
                  {{ product.rating }} ⭐（{{ product.count }}件）
                </td>
              </tr>

              <tr>
                <td class="info-label">注文数:</td>
                <td class="info-value">
                  <select class="quantity-select" v-model="quantity">
                    <option v-for="n in 10" :key="n" :value="n">
                      {{ n }}
                    </option>
                  </select>
                </td>
              </tr>

            </tbody>
          </table>

        </div>
      </div>
        
      <!-- ✅ 下段 -->
      <div class="product-overview">

        <h3 class="detail-section-title">■商品概要</h3>

        <div class="overview-content">
          <p class="overview-text">
            {{ product.description }}
          </p>
        </div>

      </div>
    <p class="form-note"><span class="required">*</span>価格に10%の消費税が含んでいます。</p>
        
      <!-- ✅ ボタン -->
      <div class="product-detail-buttons">

        <button class="btn btn-secondary btn-back" @click="$router.back()">
          戻る
        </button>

        <button class="btn btn-primary btn-add-cart" @click="addToCart(product)">
          カートに入れる
        </button>

      </div>

      </div>

    </main>
    <FooterBar />
  </div>
</template>
<script setup>
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import HeaderBar from './HeaderBar.vue'
import FooterBar from './FooterBar.vue'

const quantity = ref(1)
const route = useRoute()
const product = ref(null)

const fetchProduct = async () => {
  const res = await fetch(`https://your-backend.onrender.com/products/${route.params.id}`)
  const data = await res.json()
  product.value = formatProduct(data)
}

// const fetchProduct = async () => {
//   const res = await fetch(`https://fakestoreapi.com/products/${route.params.id}`)
//   const p = await res.json()

// const makers = ['サンプル株式会社', 'テスト工業', 'ダミー商事']

// product.value = {
//   id: p.id,
//   name: p.title,
//   price: Math.floor(p.price * 160),
//   image: p.image,
//   display_id:p.id + 171000,
//   // ✅ 追加
//   maker: makers[Math.floor(Math.random() * makers.length)],
//   rating: p.rating.rate,
//   count: p.rating.count,
//   description: p.description,
//   category: p.category
// }
// }

const formatProduct = (p) => {
  return {
    id: p.product_id,
    name: p.product_name,
    price: p.price_jpy,
    image: p.image_path,
    display_id: p.display_id,
    maker: p.maker || 'メーカー未登録',
    rating: p.rating,
    count: p.review_count,
    description: p.description,
    category: p.category
  }
}

// const addToCart = (product) => {

//   // ✅ カート用データを統一（ここが最重要）
//   const cartItem = {
//   product_id: product.id, //✅
//   quantity: 1
// }

//   let cart = JSON.parse(localStorage.getItem('cart')) || []

//   // ✅ 同じ商品があるかチェック
//   const existing = cart.find(item => item.id === cartItem.id)

//   if (existing) {
//     existing.quantity += 1
//   } else {
//     cart.push(cartItem)
//   }

//   // ✅ 保存
//   localStorage.setItem('cart', JSON.stringify(cart))
//   alert('カートに追加しました')
// }

const addToCart = async (product) => {
  try {
    const user = JSON.parse(localStorage.getItem('user'))

    const res = await fetch('https://your-backend.onrender.com/cart', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        user_id: user.user_id,
        product_id: product.id,
        quantity: quantity.value // ✅ ここ重要
      })
    })

    if (!res.ok) {
      alert('カート追加失敗')
      return
    }

    alert('カートに追加しました')

  } catch (err) {
    console.error(err)
    alert('サーバエラー')
  }
}

const formatPrice = (p) => p.toLocaleString() + '円'

onMounted(fetchProduct)


</script>