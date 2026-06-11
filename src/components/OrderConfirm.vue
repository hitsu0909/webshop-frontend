<template>
  <div class="container">

    <HeaderBar />

    <main class="main-content">

      <h2>注文確認</h2>
      <!-- ✅ ローディング -->
      <p v-if="isLoading">
        読み込み中...
      </p>

      <!-- ✅ 空 -->
      <p v-else-if="cart.length === 0" class="error-box">
        カートに商品はありません
      </p>

      <!-- ✅ 一覧 -->
      <table v-else class="cart-table">

        <thead>
          <tr>
            <th>商品</th>
            <th>ID</th>
            <th>品名</th>
            <th>単価</th>
            <th>数量</th>
            <th>小計</th>
          </tr>
        </thead>

        <tbody>

          <tr v-for="item in cart" :key="item.id">

            <td>
              <img :src="item.image" width="60" @click="goDetail(item.id)" >
            </td>

            <td>
              <p @click="goDetail(item.id)">{{ item.display_id }}</p>
            </td>

            <td>
              <p @click="goDetail(item.id)">{{ item.name }}</p>
            </td>

            <td>
              ¥{{ item.price.toLocaleString() }}
            </td>

            <td>
              {{ item.quantity }}
            </td>

            <td>
              ¥{{ (item.price * item.quantity).toLocaleString() }}
            </td>

          </tr>

        </tbody>

      </table>

      <!-- ✅ 合計 -->
      <div v-if="cart.length" class="cart-total">
        <div>合計点数：{{ totalQuantity }}点</div>
        <div>合計金額：¥{{ totalPrice.toLocaleString() }}</div>
      </div>
      <p class="form-note"><span class="required">*</span>価格に10%の消費税が含んでいます。</p>
      <br>
      <h2>届け先情報</h2>

<table class="delivery-table">
  <tbody>

    <tr>
      <th class="confirm-label">会社名</th>
      <td class="confirm-data">{{ user.company_name }}</td>

      <th class="confirm-label">会社フリガナ</th>
      <td class="confirm-data">{{ user.company_kana }}</td>
    </tr>

    <tr>
      <th class="confirm-label">担当者名</th>
      <td class="confirm-data">{{ user.user_name }}</td>

      <th class="confirm-label">担当者フリガナ</th>
      <td class="confirm-data">{{ user.user_kana }}</td>
    </tr>
<!-- 部署・郵便番号 -->
          <tr>
            <th class="confirm-label">部署名</th>
            <td class="confirm-data">{{ user.department }}</td>

            <th class="confirm-label">郵便番号</th>
            <td class="confirm-data">{{ user.postal_code }}</td>
          </tr>
    <tr>
      <th class="confirm-label">住所</th>
      <td class="confirm-data full-width" colspan="3">
        {{ user.address }}
      </td>
    </tr>

    <tr>
      <th class="confirm-label">メール</th>
      <td class="confirm-data">{{ user.mail_address }}</td>

      <th class="confirm-label">電話</th>
      <td class="confirm-data">{{ user.phone }}</td>
    </tr>

  </tbody>
</table>
 <p class="form-note">*届け先が違う場合、画面の右上のお名前をクリックし、情報を変更してください。</p>
<br>
                <h2>支払い方法</h2>
                <div class="payment-option">
                    <label class="checkbox-label">
                        <input type="checkbox" checked disabled>
                        <span class="checkbox-text">銀行振込（前払い）</span>
                    </label>
                </div>
            

            <p class="form-note">*以上の内容で注文を確定します。</p>
            <p class="form-note">よろしければ「注文確定」ボタンを押してください。</p>
      <!-- ✅ ボタン -->
      <div class="button-group">

        <button class="btn btn-secondary" @click="$router.back()">
          戻る
        </button>

        <button class="btn btn-primary" @click="confirmOrder">
          注文確定
        </button>

      </div>

    </main>

    <FooterBar />

  </div>
</template>
<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRouter } from 'vue-router'
import HeaderBar from './HeaderBar.vue'
import FooterBar from './FooterBar.vue'

const router = useRouter()
const cart = ref([])
const user = ref({})
const isLoading = ref(true)

// ✅ カート取得
onMounted(async () => {
  isLoading.value = true
  const userData = localStorage.getItem('user')
  user.value = userData ? JSON.parse(userData) : {}

  const res = await fetch(`https://webshop-backend-ejb6.onrender.com/cart/${user.value.user_id}`)
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
})
// onMounted(() => {
//   const data = localStorage.getItem('cart')
//   cart.value = data ? JSON.parse(data) : []

  
// // ユーザー情報（登録したもの）
//   const userData = localStorage.getItem('user')
//   user.value = userData ? JSON.parse(userData) : {}
// })


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

// ✅ 注文確定
const confirmOrder = async () => {
  const userData = JSON.parse(localStorage.getItem('user'))

  try {
    const res = await fetch('https://webshop-backend-ejb6.onrender.com/order', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        user_id: userData.user_id,
        cart: cart.value
      })
    })

    const data = await res.json()

    router.push({
      path: '/ordercomplete',
      query: {
        orderId: data.order_id,
        orderNumber: data.order_number,
        orderDate: data.order_date,
        items: JSON.stringify(cart.value) //✅追加
      }
    })

  } catch (err) {
    console.error(err)
    alert('サーバエラー')
  }
}

// ✅ 詳細
const goDetail = (id) => {
  router.push(`/products/${id}`)
}
</script>