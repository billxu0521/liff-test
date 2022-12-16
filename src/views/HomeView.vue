<script setup>
import { onMounted , reactive} from "vue";
import liff from "@line/liff";

const sysdata = reactive({
  language : "",
  version : "",
  isInClient : "",
  isLoggedIn : "",
  os : "",
  lineVersion : "",
}); 


onMounted(async () => {
    liff
    .init({
      liffId: "1657732539-zn43wJXG", // Use own liffId
    })
    .then(() => {
      sysdata.language = liff.getLanguage(); // String。引用 LIFF SDK 的頁面，頁面中的 lang 值
      sysdata.version = liff.getVersion(); // String。LIFF SDK 的版本
      sysdata.isInClient = liff.isInClient(); // Boolean。回傳是否由 LINE App 存取
      sysdata.isLoggedIn = liff.isLoggedIn(); // Boolean。使用者是否登入 LINE 帳號。true 時，可呼叫需要 Access Token 的 API
      sysdata.os = liff.getOS(); // String。回傳使用者作業系統：ios、android、web
      sysdata.lineVersion = liff.getLineVersion(); // 使用者的 LINE 版本
      console.log(sysdata.language,sysdata.version,sysdata.isInClient,sysdata.isLoggedIn,sysdata.os,sysdata.lineVersion);
      
    })
    .catch((err) => {
      console.log(err.code, err.message);
    });
  })
</script>

<template>
  <div>系統資訊
    <p>{{sysdata.language}}</p>
    <p>{{sysdata.version}}</p>
    <p>{{sysdata.isInClient}}</p>
    <p>{{sysdata.isLoggedIn}}</p>
    <p>{{sysdata.os}}</p>
    <p>{{sysdata.lineVersion}}</p>

  </div>
</template>
