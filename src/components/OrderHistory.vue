<template>
  <div class="container">

    <HeaderBar />

    <main class="main-content">

      <h2>注文履歴</h2>
      <!-- ✅ ローディング -->
      <p v-if="isLoading">
        読み込み中...
      </p>
      <div v-else-if="orders.length === 0" class="error-box">
        注文履歴はありません
      </div>

      <!-- ✅ 一覧表示 -->
      <table v-else class="cart-table">
        <thead>
          <tr>
            <th>注文番号</th>
            <th>注文日時</th>
            <th>点数</th>
            <th>合計金額</th>
            <th>配送状況</th>
          </tr>
        </thead>

        <tbody>

  <template v-for="order in orders" :key="order.id">

    <!-- ✅ 本体行 -->
    <tr @click="toggleMenu(order.id)" style="cursor:pointer">
      <td :title="order.orderNumber">{{ order.orderNumber }}</td>
      <td>{{ formatDateTime(order.date) }}</td>
      <td>{{ getTotalQuantity(order.items) }}点</td>
      <td>¥{{ getTotalPrice(order.items).toLocaleString() }}</td>
      <td>{{ order.status }}</td>
    </tr>

    <!-- ✅ メニュー行 -->
    <tr v-if="selectedId === order.id" class="dropdown-row">
      <td colspan="5">
        <div class="dropdown-menu">
          <div @click.stop="goDetail(order.id)">詳細確認</div>
          <div @click.stop="deleteOrder(order.id)">削除</div>
        </div>
      </td>
    </tr>

  </template>

</tbody>
      </table>
      <p class="form-note"><span class="required">*</span>金額に10%の消費税が含んでいます。</p>
      <p class="form-note"><span class="required">*</span>※行をクリックすると詳細メニューが表示されます</p>
    </main>
    <div class="button-group">
        <button class="btn btn-secondary" @click="$router.push('/products')">
          商品一覧へ
        </button>
        
        <!-- <button class="btn btn-primary" @click="$router.push('/')">
          トップへ戻る
        </button> -->
      </div>
    <FooterBar />

  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import HeaderBar from './HeaderBar.vue'
import FooterBar from './FooterBar.vue'

const router = useRouter()
const orders = ref([])
const selectedId = ref(null)
const isLoading = ref(true)
const toggleMenu = (id) => {
  selectedId.value = selectedId.value === id ? null : id
}

onMounted(async () => {
  isLoading.value = true
  const user = JSON.parse(localStorage.getItem('user'))
  
  const res = await fetch(`https://webshop-backend-ejb6.onrender.com/orders/${user.user_id}`)
  const data = await res.json()

  orders.value = data
  isLoading.value = false
})


// ✅ 日付
const formatDateTime = (date) => {
  const d = new Date(date)

  const y = d.getFullYear()
  const m = String(d.getMonth() + 1).padStart(2, '0')
  const da = String(d.getDate()).padStart(2, '0')
  const h = String(d.getHours()).padStart(2, '0')
  const mi = String(d.getMinutes()).padStart(2, '0')

  return `${y}年${m}月${da}日 ${h}:${mi}`
}

// ✅ 点数
const getTotalQuantity = (items) => {
  return items.reduce((sum, item) => sum + item.quantity, 0)
}

// ✅ 金額
const getTotalPrice = (items) => {
  return items.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  )
}

// ✅ 詳細へ
const goDetail = (id) => {
  router.push(`/orderdetail/${id}`)
}

const deleteOrder = async (id) => {
  const ok = confirm('本当に削除しますか？')
  if (!ok) return

  await fetch(`https://webshop-backend-ejb6.onrender.com/order/${id}`, {
    method: 'DELETE'
  })

  // ✅ 再取得（重要）
  loadOrders()
}

const loadOrders = async () => {
  const user = JSON.parse(localStorage.getItem('user'))

  const res = await fetch(`https://webshop-backend-ejb6.onrender.com/orders/${user.user_id}`)
  const data = await res.json()

  orders.value = data
}

// const deleteOrder = (id) => {

//   const ok = confirm('本当に削除しますか？')

//   if (!ok) return

//   // ✅ 対象削除
//   orders.value = orders.value.filter(o => o.id !== id)

//   // ✅ 保存
//   localStorage.setItem('orders', JSON.stringify(orders.value))

//   // ✅ メニュー閉じる
//   selectedId.value = null
// }

</script>