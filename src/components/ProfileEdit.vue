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
import { reactive, onMounted, ref } from 'vue'
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

const save = async () => {

  // ✅ エラー初期化
  Object.keys(errors).forEach(k => errors[k] = '')
  let ok = true

  // ✅ ① 全項目安全化
  Object.keys(form).forEach(key => {
    form[key] = (form[key] || '').toString().trim()
  })

  // ✅ ② フリガナ変換
  form.company_kana = form.company_kana.replace(/[ぁ-ん]/g, s =>
    String.fromCharCode(s.charCodeAt(0) + 0x60)
  )

  form.user_kana = form.user_kana.replace(/[ぁ-ん]/g, s =>
    String.fromCharCode(s.charCodeAt(0) + 0x60)
  )

  // ✅ ③ 入力整形（画面用）
  form.phone = form.phone.replace(/[^\d-]/g, '')
  form.postal_code = form.postal_code.replace(/[^\d-]/g, '')

  // ✅ ④ 必須チェック
  if (!form.company_name) { errors.company_name = '会社名を入力してください'; ok = false }
  if (!form.company_kana) { errors.company_kana = '会社フリガナを入力してください'; ok = false }
  if (!form.user_name) { errors.user_name = '担当者名を入力してください'; ok = false }
  if (!form.user_kana) { errors.user_kana = '担当者フリガナを入力してください'; ok = false }
  if (!form.postal_code) { errors.postal_code = '郵便番号を入力してください'; ok = false }
  if (!form.address) { errors.address = '住所を入力してください'; ok = false }
  if (!form.email) { errors.email = 'メールを入力してください'; ok = false }
  if (!form.phone) { errors.phone = '電話番号を入力してください'; ok = false }

  // ✅ フォーマットチェック
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (form.email && !emailRegex.test(form.email)) {
    errors.email = 'メール形式が正しくありません'
    ok = false
  }

  const phoneClean = form.phone.replace(/-/g, '')
  if (form.phone && !/^0\d{9,10}$/.test(phoneClean)) {
    errors.phone = '電話番号は0から始まる10〜11桁で入力してください'
    ok = false
  }

  const postalRegex = /^\d{3}-?\d{4}$/
  if (form.postal_code && !postalRegex.test(form.postal_code)) {
    errors.postal_code = '郵便番号が正しくありません（例: 123-4567,1234567）'
    ok = false
  }

  const kanaRegex = /^[ァ-ヶー　]+$/
  if (form.company_kana && !kanaRegex.test(form.company_kana)) {
    errors.company_kana = '全角カタカナで入力してください'
    ok = false
  }

  if (form.user_kana && !kanaRegex.test(form.user_kana)) {
    errors.user_kana = '全角カタカナで入力してください'
    ok = false
  }

  const invalidChars = /[<>]/
  if (invalidChars.test(form.user_name)) {
    errors.user_name = '使用できない文字が含まれています'
    ok = false
  }

  // ✅ パスワード（入力時のみ）
  if (form.password) {

    if (!form.passwordConfirm) {
      errors.passwordConfirm = 'パスワード確認を入力してください'
      ok = false
    }

    if (form.password !== form.passwordConfirm) {
      errors.passwordConfirm = '一致していません'
      ok = false
    }

    const passRegex = /^(?=.*[a-zA-Z])(?=.*\d).{6,}$/
    if (!passRegex.test(form.password)) {
      errors.password = '英字と数字を含めて6文字以上'
      ok = false
    }
  }

  if (!ok) return

  try {
    const user = JSON.parse(localStorage.getItem('user'))

    // ✅ ✅ ✅ ここが今回の追加ポイント🔥
    const dataToSend = { ...form }

    // ✅ ハイフン削除（DB統一）
    dataToSend.phone = (dataToSend.phone || '').replace(/-/g, '')
    dataToSend.postal_code = (dataToSend.postal_code || '').replace(/-/g, '')

    // ✅ パスワード未入力なら削除
    if (!form.password) {
      delete dataToSend.password
    }

    const res = await fetch(
      `https://webshop-backend-ejb6.onrender.com/user/${user.user_id}`,
      {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(dataToSend)
      }
    )

    if (!res.ok) {
      alert('更新失敗')
      return
    }

    const updatedUser = { ...user, ...dataToSend }
    localStorage.setItem('user', JSON.stringify(updatedUser))

    router.push('/profile')

  } catch (err) {
    console.error(err)
    alert('サーバエラー')
  }
}

</script>
