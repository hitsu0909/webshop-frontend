<template>
  <div class="container">

    <HeaderBar />

    <main class="main-content">

      <h2>カート</h2>
      <!-- ✅ ローディング -->
      <p v-if="isLoading">
        読み込み中...
      </p>

      <!-- ✅ 空 -->
      <p v-else-if="cart.length === 0" class="error-box">
        カートに商品はありません
      </p>

      <!-- ✅ 商品一覧 -->
      <table v-else class="cart-table">

        <thead>
          <tr>
            <th>商品</th>
            <th>ID</th>
            <th>品名</th>
            <th>単価</th>
            <th>数量</th>
            <th>小計</th>
            <th>操作</th>
          </tr>
        </thead>

        <tbody>

          <tr v-for="item in cart" :key="item.id">

            <!-- 商品 -->
            <td>
              <img :src="item.image" width=60 @click="goDetail(item.id)" style="cursor">
            </td>

            <td>
              <p @click="goDetail(item.id)">{{ item.display_id }}</p>
            </td>

            <td>
              <p @click="goDetail(item.id)">{{ item.name }}</p>
            </td>

            <!-- 価格 -->
            <td>
              ¥{{ item.price.toLocaleString() }}
            </td>

            <!-- 数量 -->
            <td>
              <select v-model.number="item.quantity" @change="updateQuantity(item)">
                <option v-for="n in 20" :key="n" :value="n">
                  {{ n }}
                </option>
              </select>
            </td>

            <!-- 小計 -->
            <td>
              ¥{{ (item.price * item.quantity).toLocaleString() }}
            </td>

            <!-- 削除 -->
            <td>
              <button @click="removeItem(item.detailId)">
                削除
              </button>
            </td>

          </tr>

        </tbody>

      </table>

      <!-- ✅ 合計 -->
      <div class="cart-total">
        <div>合計点数：{{ totalQuantity }}点</div>
        <div>合計金額：¥{{ totalPrice.toLocaleString() }}</div>
      </div>
      <p class="form-note"><span class="required">*</span>価格に10%の消費税が含んでいます。</p>
      <!-- ✅ エラーメッセージ -->
        <p v-if="errorMessage" class="error-box">
          {{ errorMessage }}
        </p>
      <!-- ✅ ボタン -->
      <div class="button-group">

        <button class="btn btn-secondary" @click="$router.push('/products')">
          商品一覧へ
        </button>

        <button class="btn btn-primary" @click="goOrder">
          注文へ進む
        </button>

      </div>
        
    </main>

    <FooterBar />

  </div>
</template>
<script setup>
import { ref, onMounted, computed } from 'vue'
import HeaderBar from './HeaderBar.vue'
import FooterBar from './FooterBar.vue'
import { useRoute } from 'vue-router'
import { useRouter } from 'vue-router'

const router = useRouter()
const cart = ref([])
const route = useRoute()
const errorMessage = ref('')
const isLoading = ref(true)

const goOrder = () => {

  if (cart.value.length === 0) {
    errorMessage.value = 'カートに商品を追加してください。'
    return
  }

  router.push('/orderconfirm')
}
// ✅ 読み込み
// onMounted(() => {
//   const data = localStorage.getItem('cart')
//   cart.value = data ? JSON.parse(data) : []
// })

// ✅ 更新
// const updateCart = () => {
//   localStorage.setItem('cart', JSON.stringify(cart.value))
// }

const updateQuantity = async (item) => {
  await fetch('https://webshop-backend-ejb6.onrender.com/cart', {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      cart_detail_id: item.detailId,
      quantity: item.quantity
    })
  })
}

// ✅ 削除
// const removeItem = (id) => {
//   cart.value = cart.value.filter(item => item.id !== id)
//   updateCart()
// }
const removeItem = async (detailId) => {
  await fetch(`https://webshop-backend-ejb6.onrender.com/cart/${detailId}`, {
    method: 'DELETE'
  })

  // 再取得
  loadCart()
}

const loadCart = async () => {
  isLoading.value = true

  const user = JSON.parse(localStorage.getItem('user'))

  const res = await fetch(`https://webshop-backend-ejb6.onrender.com/cart/${user.user_id}`)
  const data = await res.json()

  cart.value = data.map(item => ({
    detailId: item.cart_detail_id,
    id: item.product_id,
    display_id: item.display_id,
    name: item.product_name,
    price: item.price_jpy,
    image: item.image_path,
    quantity: item.quantity
  }))

  isLoading.value = false
}

onMounted(loadCart)

// ✅ 合計
const totalPrice = computed(() => {
  return cart.value.reduce((sum, item) => {
    return sum + item.price * item.quantity
  }, 0)
})

const totalQuantity = computed(() => {
  return cart.value.reduce((sum, item) => {
    return sum + item.quantity
  }, 0)
})

// ✅ 詳細
const goDetail = (id) => {
  router.push(`/products/${id}`)
}
</script>
