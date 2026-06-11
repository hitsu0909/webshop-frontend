<template>
  <div class="container">
    <HeaderBar />
    <h2 class="product-detail-title">
          商品一覧
        </h2>
<!-- ✅ 検索バー -->
    <div class="search-bar">

      <!-- キーワード -->
      <input
        v-model="keyword"
        class="search-input"
        placeholder="商品・メーカーを入力してください"
      />

      <!-- カテゴリ -->
      <!-- <select class="search-select" v-model="selectedCategory">
        <option value="">すべて</option>
        <option v-for="c in categories" :key="c" :value="c">
          {{ c }}
        </option>
      </select> -->
      <select class="search-select" v-model="selectedCategory">
        <option value="">すべて</option>
        <option v-for="c in categories" :key="c" :value="c">
          {{ c }}
        </option>
      </select>

      <!-- 検索 -->
      <button class="search-button" @click="searchProducts">
        検索
      </button>

      <!-- 並び替え -->
      <select class="search-select" v-model="sortOrder" @change="applyFilters">
        <option value="recommend">おすすめ順</option>
        <option value="asc">価格：安い順</option>
        <option value="desc">価格：高い順</option>
        <option value="rating">評価順</option>
        <option value="count">レビュー件数順</option>
      </select>

      <!-- 更新 -->
      <!-- <button class="search-button" @click="fetchProducts">
        更新
      </button> -->

    </div>

    <!-- ✅ メイン -->
    <main class="main-content">

      <!-- ✅ 件数 -->
      <p class="search-result">
        検索結果：{{ filteredProducts.length }}件
      </p>

      <!-- ✅ 商品一覧 -->
      <div class="product-grid">

        <div
          v-for="item in filteredProducts"
          :key="item.id"
          class="product-card"
        >

          <img :src="item.image" width=100 @click="goDetail(item.id)">
        <div class="product-info">
          <p @click="goDetail(item.id)" :title="item.name">
            {{ item.name }}
          </p>

          <p>{{ item.maker }}</p>

          <p>¥{{ item.price.toLocaleString() }}</p>

          <p>⭐ {{ item.rating }}（{{ item.count }}件）</p>
          </div>
          <button @click="addToCart(item)">
            カートに入れる
          </button>
          
        </div>

      </div>
      <p class="form-note"><span class="required">*</span>価格に10%の消費税が含んでいます。</p>
    </main>
  <FooterBar />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import HeaderBar from './HeaderBar.vue'
import FooterBar from './FooterBar.vue'

const router = useRouter()

const productsOriginal = ref([])
const filteredProducts = ref([])
const categories = ref([])

const keyword = ref('')
const selectedCategory = ref('')
const sortOrder = ref('recommend')

// ✅ 仮メーカー
// const makers = ['サンプル株式会社', 'テスト工業', 'ダミー商事']

// ✅ 商品取得
const fetchProducts = async () => {
  const res = await fetch('http://localhost:3000/products')
  const data = await res.json()
  productsOriginal.value = formatProducts(data)
  applyFilters()
}

// ✅ カテゴリ取得
const fetchCategories = async () => {
  const res = await fetch('http://localhost:3000/categories')
  const data = await res.json()
  categories.value = data
  // categories.value = await res.json()
}

const loadCategory = async (category) => {
  try {
    let url = ''

    if (!category) {
      // ✅ すべて
      url = 'http://localhost:3000/products'
    } else {
      // ✅ カテゴリ指定
      url = `http://localhost:3000/products/category/${category}`
    }

    const res = await fetch(url)
    const data = await res.json()

    productsOriginal.value = formatProducts(data)
    applyFilters()
  } catch (err) {
    console.error('取得エラー:', err)
  }
}

const formatProducts = (data) => {
  return data.map(p => ({
    id: p.product_id,
    name: p.product_name,
    price: p.price_jpy,
    image: p.image_path,
    display_id: p.display_id,
    maker: p.maker || 'メーカー未登録',
    rating: p.rating,
    count: p.review_count,
    category: p.category
  }))
}

// ✅ フィルタ処理
const applyFilters = () => {

  let result = [...productsOriginal.value]

  const key = keyword.value.toLowerCase()

  // ✅ 検索（商品＋メーカー）
  if (keyword.value) {
    result = result.filter(p =>
      p.name.toLowerCase().includes(key) ||
      p.maker.toLowerCase().includes(key)
    )
  }

  // ✅ 並び替え
  if (sortOrder.value === 'asc') {
    result.sort((a, b) => a.price - b.price)
  }
  else if (sortOrder.value === 'desc') {
    result.sort((a, b) => b.price - a.price)
  }
  else if (sortOrder.value === 'rating') {
    result.sort((a, b) => b.rating - a.rating)
  }
  else if (sortOrder.value === 'count') {
    result.sort((a, b) => b.count - a.count)
  }
  else {
    result.sort((a, b) => a.id - b.id)
  }

  filteredProducts.value = result
}

// ✅ 詳細
const goDetail = (id) => {
  router.push(`/products/${id}`)
}

const addToCart = async (product) => {
  try {
    const user = JSON.parse(localStorage.getItem('user'))

    const res = await fetch('http://localhost:3000/cart', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        user_id: user.user_id,
        product_id: product.id,
        quantity: 1
      })
    })

    if (!res.ok) {
      alert('追加失敗')
      return
    }

    alert('カートに追加しました')

  } catch (err) {
    console.error(err)
    alert('サーバエラー')
  }
}

onMounted(() => {
  fetchProducts()
  fetchCategories()
})

const searchProducts = async () => {
  // ✅ カテゴリAPI取得
  await loadCategory(selectedCategory.value)

  // ✅ そのあとフィルタ（キーワード & ソート）
  applyFilters()
}
</script>
