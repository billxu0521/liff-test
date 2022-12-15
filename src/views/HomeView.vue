<script setup>
import { onMounted } from "vue";
import liff from "@line/liff";

onMounted(async () => {
    liff
    .init({
      liffId: "1657732539-zn43wJXG", // Use own liffId
    })
    .then(() => {
      if (!liff.isLoggedIn()) liff.login({ redirectUri: window.location.href });
      const language = liff.getLanguage(); // String。引用 LIFF SDK 的頁面，頁面中的 lang 值
      const version = liff.getVersion(); // String。LIFF SDK 的版本
      const isInClient = liff.isInClient(); // Boolean。回傳是否由 LINE App 存取
      const isLoggedIn = liff.isLoggedIn(); // Boolean。使用者是否登入 LINE 帳號。true 時，可呼叫需要 Access Token 的 API
      const os = liff.getOS(); // String。回傳使用者作業系統：ios、android、web
      const lineVersion = liff.getLineVersion(); // 使用者的 LINE 版本
    })
    .catch((err) => {
      console.log(err.code, err.message);
    });
  })
</script>

<template>
  <div>
    <p>{{language}}</p>
    <p>{{version}}</p>
    <p>{{isInClient}}</p>
    <p>{{isLoggedIn}}</p>
    <p>{{os}}</p>
    <p>{{lineVersion}}</p>

  </div>
</template>
