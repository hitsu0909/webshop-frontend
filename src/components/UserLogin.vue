<template>
  <div class="container">

    <header class="header">
      <h1 class="site-title">海外商品の通販サイト</h1>
      <p class="site-description">
        品揃えがいいお店
      </p>
    </header>

    <main class="main-content">

      <h2>・法人会員ログイン</h2>

      <div class="hmsg">
        
      </div>

      <form @submit.prevent="login">

        <table class="form-table">
          <tbody>
          <tr>
            <th>メールアドレス</th>
            <td><input type="email" v-model="mail" /></td>
          </tr>
          <tr>
            <th>パスワード</th>
            <td><input type="password" v-model="password" /></td>
          </tr>
          </tbody>
        </table>

        <p v-if="errorMsg" class="error">{{ errorMsg }}</p>

        <div class="button-group">
          <button class="btn btn-primary" type="submit">ログイン</button>
        </div>

      </form>
      <p class="form-note">
        パスワードをお忘れの場合は、管理者までご連絡ください。
      </p>
      <hr />

      <h3>初めての方</h3>
      <p>会員がない方は、新規登録をお願いいたします。</p>
      <button class="btn btn-secondary" @click="$router.push('/register')">
        新規登録
      </button>

    </main>
<FooterBar />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import FooterBar from './FooterBar.vue'
const router = useRouter()

const mail = ref('')
const password = ref('')
const errorMsg = ref('')

// const login = () => {
//   errorMsg.value = ''

//   const user = JSON.parse(localStorage.getItem('user'))

//   if (!user || user.email !== mail.value || user.password !== password.value) {
//     errorMsg.value = 'メールアドレスまたはパスワードが正しくありません'
//     return
//   }

//   // ✅ 保存 → 遷移
//   localStorage.setItem('isLogin', JSON.stringify(true))
//   router.push('/products')

//   // console.log('入力メール', mail.value)
//   // console.log('入力パス', password.value)
//   // console.log('保存user', user)

// }

const login = async () => {
  errorMsg.value = ''

  try {
    const res = await fetch('https://webshop-backend-ejb6.onrender.com/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        email: mail.value,
        password: password.value
      })
    })

    if (!res.ok) {
      errorMsg.value = 'メールアドレスまたはパスワードが正しくありません'
      return
    }

    const user = await res.json()

    // ✅ ログイン状態保存
    localStorage.setItem('user', JSON.stringify(user))
    localStorage.setItem('isLogin', JSON.stringify(true))

    router.push('/products')
  } catch (err) {
    console.error(err)
    errorMsg.value = 'サーバエラー'
  }
}
</script>