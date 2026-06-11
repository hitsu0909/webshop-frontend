<template>
  <div class="container">

    <HeaderBar />

    <main class="main-content">

      <h2 class="form-title">■顧客情報変更</h2>

      <form class="customer-form" @submit.prevent="save">

        <table class="form-table">
          <tbody>

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
                <input type="tel" v-model="form.postal_code" />
                <p class="error">{{ errors.postal_code }}</p>
              </td>
            </tr>

            <!-- 住所（横幅フル） -->
            <tr>
              <th class="form-label">会社住所<span class="required">*</span></th>
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
                <input type="tel" v-model="form.phone" />
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

              <th class="form-label">確認<span class="required">*</span></th>
              <td class="form-input">
                <input type="password" v-model="form.passwordConfirm" />
                <p class="error">{{ errors.passwordConfirm }}</p>
              </td>
            </tr>
          </tbody>
        </table>
        <p class="form-note"><span class="required">*</span>項目は必須ですが、変更必要な項目だけを変更してください。</p>
        <div class="button-group">
          <button type="button" class="btn btn-secondary" @click="$router.back()">
            戻る
          </button>

          <button class="btn btn-primary">
            保存
          </button>
        </div>

      </form>

    </main>

    <FooterBar />

  </div>
</template>

<script setup>
import { reactive, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import HeaderBar from './HeaderBar.vue'
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
  phone: ''
})

// ✅ 初期値セット
onMounted(() => {
  const data = localStorage.getItem('user')

  if (data) {
    const user = JSON.parse(data)

    Object.assign(form, user)

    // ✅ パスワード初期値（空）
    form.password = ''
    form.passwordConfirm = ''
  }
})

// ✅ 保存処理
const errors = reactive({})

// const save = () => {

//   // 初期化
//   Object.keys(errors).forEach(k => errors[k] = '')

//   let ok = true

//   if (!form.company_name) {
//     errors.company_name = '必須です'
//     ok = false
//   }

//   if (!form.company_kana) {
//     errors.company_kana = '必須です'
//     ok = false
//   }

//   if (!form.user_name) {
//     errors.user_name = '必須です'
//     ok = false
//   }

//   if (!form.user_kana) {
//     errors.user_kana = '必須です'
//     ok = false
//   }

//   if (!form.postal_code) {
//     errors.postal_code = '必須です'
//     ok = false
//   }

//   if (!form.address) {
//     errors.address = '必須です'
//     ok = false
//   }

//   if (!form.email) {
//     errors.email = '必須です'
//     ok = false
//   }

//   if (!form.phone) {
//     errors.phone = '必須です'
//     ok = false
//   }

//   if (!form.password) {
//     errors.password = '必須です'
//     ok = false
//   }

//   if (!form.passwordConfirm) {
//     errors.passwordConfirm = '必須です'
//     ok = false
//   }

//   if (form.password !== form.passwordConfirm) {
//     errors.passwordConfirm = '一致していません'
//     ok = false
//   }

//   if (!ok) return

//   // ✅ 保存
//   localStorage.setItem('user', JSON.stringify(form))

//   // ✅ 確認画面へ
//   router.push('/profile')

// }

const save = async () => {
  Object.keys(errors).forEach(k => errors[k] = '')
  let ok = true

  // ✅ バリデーション（そのまま）
  if (!form.company_name) { errors.company_name = '必須です'; ok = false }
  if (!form.company_kana) { errors.company_kana = '必須です'; ok = false }
  if (!form.user_name) { errors.user_name = '必須です'; ok = false }
  if (!form.user_kana) { errors.user_kana = '必須です'; ok = false }
  if (!form.postal_code) { errors.postal_code = '必須です'; ok = false }
  if (!form.address) { errors.address = '必須です'; ok = false }
  if (!form.email) { errors.email = '必須です'; ok = false }
  if (!form.phone) { errors.phone = '必須です'; ok = false }

  if (!ok) return

  try {
    const user = JSON.parse(localStorage.getItem('user'))

    const res = await fetch(`http://localhost:3000/user/${user.user_id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form)
    })

    if (!res.ok) {
      alert('更新失敗')
      return
    }

    // ✅ 最新情報でlocalStorage更新
    const updatedUser = { ...user, ...form }
    localStorage.setItem('user', JSON.stringify(updatedUser))

    router.push('/profile')

  } catch (err) {
    console.error(err)
    alert('サーバエラー')
  }
}
</script>
