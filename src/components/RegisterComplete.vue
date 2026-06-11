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

      <h2>・登録完了</h2>

      <div class="notice-box">
        <p>お疲れ様でした。会員登録が完了しました。</p>
        <p>この度は、海外商品の通販サイトにご登録いただきまして誠にありがとうございます。</p>
        <p>登録いただいたメールアドレス宛に、確認メールを送信いたしました。</p>
      </div>

      <div class="notice-box">
        
      <h3>■ メールが届かない場合</h3>
      <ul>
        <p>迷惑メールフォルダを確認してください。</p>
        <!-- <li>アドレスの誤りを確認</li> -->
      </ul>
      <br>
      <h3>■ ご利用について</h3>
      <p>ログイン後すぐに利用できます。</p>

      </div>


      <!-- ボタン -->
      <div class="button-group">
      <button class="btn btn-primary" @click="$router.push('/userlogin')">
        ログインへ
      </button>
      </div>
    </main>
    <FooterBar />
  </div>
</template>

<script setup>
// import HeaderBar from './HeaderBar.vue'
import FooterBar from './FooterBar.vue'
</script>
