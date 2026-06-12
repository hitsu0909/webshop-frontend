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
      <h2 class="form-title">○顧客情報入力</h2>

      <form class="customer-form" @submit.prevent="validate">

        <table class="form-table">
          <tbody>
            <!-- 会社 -->
            <tr>
              <th class="form-label">会社名<span class="required">*</span></th>
              <td class="form-input">
                <input v-model="form.company_name" />
                <p class="error">{{ errors.company_name }}</p>
              </td>

              <th class="form-label">会社フリガナ<span class="required">*</span></th>
              <td class="form-input">
                <input v-model="form.company_kana" />
                <p class="error">{{ errors.company_kana }}</p>
              </td>
            </tr>
            
            <!-- 担当者 -->
            <tr>
              <th class="form-label">担当者名<span class="required">*</span></th>
              <td class="form-input">
                <input v-model="form.user_name" />
                <p class="error">{{ errors.user_name }}</p>
              </td>

              <th class="form-label">担当者フリガナ<span class="required">*</span></th>
              <td class="form-input">
                <input v-model="form.user_kana" />
                <p class="error">{{ errors.user_kana }}</p>
              </td>
            </tr>

            <!-- 部署・郵便番号 -->
            <tr>
              <th class="form-label">部署名</th>
              <td class="form-input">
                <input v-model="form.department" />
                <!-- <p class="error">{{ errors.department }}</p> -->
              </td>

              <th class="form-label">郵便番号<span class="required">*</span></th>
              <td class="form-input">
                <input v-model="form.postal_code" inputmode="numeric" />
                <p class="error">{{ errors.postal_code }}</p>
              </td>
            </tr>

            <!-- 住所（横幅フル） -->
            <tr>
              <th class="form-label">住所<span class="required">*</span></th>
              <td class="full-width" colspan="3">
                <input v-model="form.address" />
                <p class="error">{{ errors.address }}</p>
              </td>
            </tr>

            <!-- メール・電話 -->
            <tr>
              <th class="form-label">メール<span class="required">*</span></th>
              <td class="form-input">
                <input type="email" v-model="form.email" />
                <p class="error">{{ errors.email }}</p>
              </td>

              <th class="form-label">電話番号<span class="required">*</span></th>
              <td class="form-input">
                <input v-model="form.phone" inputmode="numeric" />
                <p class="error">{{ errors.phone }}</p>
              </td>
            </tr>

            <!-- パスワード -->
            <tr>
              <th class="form-label">パスワード<span class="required">*</span></th>
              <td class="form-input">
                <input type="password" v-model="form.password" />
                <p class="error">{{ errors.password }}</p>
              </td>

              <th class="form-label">パスワード確認<span class="required">*</span></th>
              <td class="form-input">
                <input type="password" v-model="form.passwordConfirm" />
                <p class="error">{{ errors.passwordConfirm }}</p>
              </td>
            </tr>

          </tbody>
        </table>
        <p class="form-note"><span class="required">*</span>入力必須項目です。</p>
        
        <div class="button-group">
          <button type="submit" class="btn btn-primary">確認</button>
          <button type="button" class="btn btn-secondary" @click="$router.back()">
            戻る
          </button>
        </div>


      </form>
    </main>
    <FooterBar />
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'
import { useRouter } from 'vue-router'
// import HeaderBar from './HeaderBar.vue'
import FooterBar from './FooterBar.vue'

const router = useRouter()

const form = reactive({
  company_name: '',
  company_kana: '',
  user_name: '',
  user_kana: '',
  department: '',
  postal_code: '',
  address: '',
  email: '',
  phone: '',
  password: '',
  passwordConfirm: ''
})

const errors = reactive({})
const loading = ref(false)
let ok = true
const validate = async () => {
  Object.keys(errors).forEach(k => errors[k] = '')

  

  if (!form.company_name) {
    errors.company_name = '会社名を入力してください'
    ok = false
  }

  if (!form.company_kana) {
    errors.company_kana = '会社フリガナを入力してください'
    ok = false
  }

  if (!form.user_name) {
    errors.user_name = '担当者名を入力してください'
    ok = false
  }

  if (!form.user_kana) {
    errors.user_kana = '担当者フリガナを入力してください'
    ok = false
  }

  if (!form.postal_code) {
    errors.postal_code = '郵便番号を入力してください'
    ok = false
  }

  if (!form.address) {
    errors.address = '住所を入力してください'
    ok = false
  }

  if (!form.email) {
    errors.email = 'メールを入力してください'
    ok = false
  }

  if (!form.phone) {
    errors.phone = '電話番号を入力してください'
    ok = false
  }

  if (!form.password) {
    errors.password = 'パスワードを入力してください'
    ok = false
  }

  if (!form.passwordConfirm) {
    errors.passwordConfirm = 'パスワード確認を入力してください'
    ok = false
  }

  if (form.password !== form.passwordConfirm) {
    errors.passwordConfirm = '一致していません'
    ok = false
  }

  if (loading.value) return
  loading.value = true

  // validation...

  if (!ok) {
    loading.value = false
    return
  }

  loading.value = false
  
// ✅ 確認画面へデータ渡す
  if (!ok) return
localStorage.setItem('form', JSON.stringify(form))
router.push('/confirm')
}

const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/

if (!emailRegex.test(form.email)) {
  errors.email = 'メール形式が正しくありません'
  ok = false
}

const phoneRegex = /^[0-9-]{10,13}$/

if (!phoneRegex.test(form.phone)) {
  errors.phone = '電話番号が正しくありません'
  ok = false
}

const postalRegex = /^\d{3}-?\d{4}$/

if (!postalRegex.test(form.postal_code)) {
  errors.postal_code = '郵便番号が正しくありません（例: 123-4567）'
  ok = false
}

const passRegex = /^(?=.*[a-zA-Z])(?=.*\d).{6,}$/

if (!passRegex.test(form.password)) {
  errors.password = '英字と数字を含めて6文字以上'
  ok = false
}

form.postal_code = form.postal_code.replace(/[^\d-]/g, '')
form.phone = form.phone.replace(/[^\d-]/g, '')

Object.keys(form).forEach(key => {
  if (typeof form[key] === 'string') {
    form[key] = form[key].trim()
  }
})

const invalidChars = /[<>]/

if (invalidChars.test(form.user_name)) {
  errors.user_name = '使用できない文字が含まれています'
  ok = false
}

</script>