  <template>
    <header class="header">

      <div class="header-top">

        <h1 class="site-title" @click="goHome">海外商品の法人通販サイト</h1>
        <p class="site-description">
          品揃えがいい、個人の事業者様でも手軽に仕入が可能な激安卸問屋
        </p>

        <!-- ✅ 右上エリア -->
        <div class="user-info" v-show="isLogin && user" ref="menuRef">
          
          <p class="form-note">
            ※名前をクリックするとメニューが表示されます
          </p>

          <div @click.stop="toggleMenu">
          <p class="company-name">{{ user?.company_name }}</p>
          <p class="user-name">{{ user?.user_name }}　様▼</p>
          </div>
          
          
          <!-- ✅ メニュー追加 -->
          <div v-if="isOpen" class="dropdown">
            <div @click="goProfile">顧客情報確認</div>
            <div @click="goEdit">顧客情報変更</div>
          </div>

          <div class="header-links">
            <a @click="goCart">カート</a>
            <a @click="goHistory">注文履歴</a>
            <a @click="logout">ログアウト</a>
          </div>

        </div>
      </div>

    </header>
  </template>
  <script setup>
  import { ref, onMounted, onUnmounted } from 'vue'
  import { useRoute,useRouter } from 'vue-router'
import { computed } from 'vue'

  const route = useRoute()
  const router = useRouter()
  // const user = ref(null)
  // const isLogin = ref(false)
  const isOpen = ref(false)
  const menuRef = ref(null)

  const user = ref(null)
  const isLogin = ref(false)


  
onMounted(() => {
  const storedUser = localStorage.getItem('user')
  user.value = storedUser ? JSON.parse(storedUser) : null

  const storedLogin = localStorage.getItem('isLogin')
  isLogin.value = storedLogin ? JSON.parse(storedLogin) : false
})



  onUnmounted(() => {
    document.removeEventListener('click', handleClickOutside)
    document.removeEventListener('keydown', handleKeydown)
  })


  const showMenu = ref(false)

  // ✅ 開閉
  const toggleMenu = () => {
    isOpen.value = !isOpen.value
  }

const handleClickOutside = (e) => {
  if (!menuRef.value) return

  const el = menuRef.value

  if (!el.contains(e.target)) {
    isOpen.value = false
  }
}


const handleKeydown = (e) => {
  if (e.key === 'Escape') {
    isOpen.value = false
  }
}

  // ✅ 遷移
  const goProfile = () => {
    router.push('/profile')
  }

  const goEdit = () => {
    router.push('/profileedit')
  }

  // ✅ カート
  const goCart = () => {
    router.push('/cart')
  }

  // ✅ 履歴
  const goHistory = () => {
    router.push('/orderhistory')
  }


  // ✅ ログアウト
  const logout = () => {
    localStorage.removeItem('isLogin')
    localStorage.removeItem('user') //✅追加
    router.push('/userlogin')
  }

  const goHome = () => {
    const storedUser = localStorage.getItem('user')
    user.value = storedUser ? JSON.parse(storedUser) : null

    const storedLogin = localStorage.getItem('isLogin')
    isLogin.value = storedLogin ? JSON.parse(storedLogin) : false
    // const isLogin = JSON.parse(localStorage.getItem('isLogin'))

    if (isLogin) {
      router.push('/products')
    } else {
  router.push('/userlogin')
    }
  }
  </script>
