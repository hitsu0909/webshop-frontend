<template>
  <div class="container">
    <!-- <HeaderBar /> -->
     <header class="header">
      <h1 class="site-title">海外商品の通販サイト</h1>
      <p class="site-description">
        品揃えがいいお店
      </p>
    </header>
    <main class="main-content">
      <h2 class="form-title">△登録情報確認</h2>

      <table class="confirm-table">
         <tbody>
          
<!-- 会社 -->
          <tr>
            <th class="confirm-label">会社名</th>
            <td class="confirm-data">{{ form.company_name }}</td>

            <th class="confirm-label">会社フリガナ</th>
            <td class="confirm-data">{{ form.company_kana }}</td>
          </tr>

          <!-- 担当 -->
          <tr>
            <th class="confirm-label">担当者名</th>
            <td class="confirm-data">{{ form.user_name }}</td>

            <th class="confirm-label">担当者フリガナ</th>
            <td class="confirm-data">{{ form.user_kana }}</td>
          </tr>

          <!-- 部署・郵便番号 -->
          <tr>
            <th class="confirm-label">部署名</th>
            <td class="confirm-data">{{ form.department }}</td>

            <th class="confirm-label">郵便番号</th>
            <td class="confirm-data">{{ form.postal_code }}</td>
          </tr>

          <!-- 住所（横長） -->
          <tr>
            <th class="confirm-label">会社住所</th>
            <td class="confirm-data full-width" colspan="3">
              {{ form.address }}
            </td>
          </tr>

          <!-- メール・電話 -->
          <tr>
            <th class="confirm-label">メール</th>
            <td class="confirm-data">{{ form.email }}</td>

            <th class="confirm-label">電話番号</th>
            <td class="confirm-data">{{ form.phone }}</td>
          </tr>

          <!-- パスワード -->
          <tr>
            <th class="confirm-label">パスワード</th>
            <td class="confirm-data">{{ maskedPassword }}</td>

            <th class="confirm-label">確認</th>
            <td class="confirm-data">{{ maskedPassword }}</td>
          </tr>

        </tbody> 
      </table>

      
      <p class="form-note"><span class="required">*</span>以上の内容をご確認ください。よろしければ、「登録」ボタンをクリックしてください。</p>

      <!-- ボタン -->
      <div class="button-group">

        <button class="btn btn-primary" @click="register">
          登録
        </button>

        <button class="btn btn-secondary" @click="$router.back()">
          修正
        </button>

      </div>


    </main>
    <FooterBar />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import FooterBar from './FooterBar.vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const form = ref({})

// ✅ データ受け取り（ここ重要）
onMounted(() => {
  const data = localStorage.getItem('form')

  if (!data) {
    alert('入力画面から来てください')
    return
  }

  form.value = JSON.parse(data)
})

// ✅ パスワードを＊＊＊＊にする
const maskedPassword = computed(() => {
  return form.value.password
    ? '*'.repeat(form.value.password.length)
    : ''
})

// const register = () => {
//   localStorage.setItem('user', JSON.stringify(form.value))

//   // ✅ 完了画面へ遷移
//   router.push('/complete')
// }

const register = async () => {
  try {

    // ✅ データ整形（ここ重要🔥）
    const phone = (form.value.phone || '').replace(/-/g, '')
    const postal_code = (form.value.postal_code || '').replace(/-/g, '')

    const res = await fetch('https://webshop-backend-ejb6.onrender.com/register', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        company_name: form.value.company_name,
        company_kana: form.value.company_kana,
        user_name: form.value.user_name,
        user_kana: form.value.user_kana,
        department: form.value.department,
        postal_code: postal_code,   // ✅ 統一済データ
        address: form.value.address,
        email: form.value.email,
        phone: phone,              // ✅ 統一済データ
        password: form.value.password
      })
    })

    if (!res.ok) {
      alert('登録失敗')
      return
    }

    // ✅ ローカル削除
    localStorage.removeItem('form')

    router.push('/complete')

  } catch (err) {
    console.error(err)
    alert('サーバエラー')
  }
}
</script>